<properties
	pageTitle="Verwenden von Azure Media Video Thumbnails zum Erstellen einer Videozusammenfassung"
	description="Die Videozusammenfassung kann Ihnen dabei helfen, Zusammenfassungen von langen Videos durch das automatische Auswählen von interessanten Ausschnitten aus dem Quellvideo zu erstellen. Dies ist hilfreich, wenn Sie einen schnellen Überblick darüber bieten wollen, was man in einem langen Video zu sehen bekommt."
	services="media-services"
	documentationCenter=""
	authors="juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/14/2016"   
	ms.author="milanga;juliako;"/>

#Verwenden von Azure Media Video Thumbnails zum Erstellen einer Videozusammenfassung
##Übersicht

Mit dem **Azure Media Video Thumbnails**-Medienprozessor können Sie eine Zusammenfassung eines Videos erstellen. Das ist hilfreich für Kunden, die nur eine zusammenfassende Vorschau eines langen Videos ansehen möchten. Kunden möchten zum Beispiel nur eine kurze „Videozusammenfassung“ sehen, wenn sie mit der Maus auf eine Miniaturansicht zeigen. Durch die Anpassung der Parameter von **Azure Media Video Thumbnails** über eine Konfigurationsvoreinstellung können Sie die leistungsstarken Technologien des Medienprozessors für die Szenenerkennung und für das Zusammenfügen von Aufnahmen des Medienprozessors verwenden, um algorithmisch einen beschreibenden Subclip zu erstellen.

Der **Azure Media Video Thumbnails**-Medienprozessor befindet sich derzeit in der Vorschauphase.

Dieses Thema enthält Details zu **Azure Media Video Thumnnails** und zeigt, wie sie mit Media Services SDK für .NET verwendet wird.

##Beispiel einer Videozusammenfassung 

Hier sind einige Beispiele für die Fähigkeiten des Azure Media Video Thumbnails-Medienprozessors :

###Original-Video

[Original-Video](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Faed33834-ec2d-4788-88b5-a4505b3d032c%2FMicrosoft%27s%20HoloLens%20Live%20Demonstration.ism%2Fmanifest)

###Ergebnis der Video-Miniaturansicht

[Ergebnis der Video-Miniaturansicht](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Ff5c91052-4232-41d4-b531-062e07b6a9ae%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

##Taskkonfiguration (Voreinstellung)

Beim Erstellen eines Video-Miniaturansichten-Tasks mit **Azure Media Video Thumbnails** müssen Sie eine Voreinstellungskonfiguration angeben. Das obige Miniaturansicht-Beispiel wurde mit der folgenden JSON-Basiskonfiguration erstellt:

	{"version":"1.0"}

Derzeit können Sie die folgenden Parameter ändern:

Param|Beschreibung
---|---
outputAudio|Gibt an, ob das resultierende Video Audiodaten enthält oder nicht. <br/>Zulässige Werte sind: TRUE oder FALSE. Der Standardwert ist TRUE.
fadeInFadeOut|Gibt an, ob Übergänge zwischen den separaten bewegten Miniaturansichten verwendet werden oder nicht. <br/>Zulässige Werte sind: TRUE oder FALSE. Der Standardwert ist TRUE.
maxMotionThumbnailDurationInSecs|Ganze Zahl, die angibt, wie lange das gesamte fertige Video sein soll. Der Standardwert hängt von der ursprünglichen Videodauer ab.


Die folgende Tabelle beschreibt die Standarddauer, wenn **maxMotionThumbnailInSecs** nicht verwendet wird.

||||
---|---|---|---|---
Videodauer|d < 3 Minuten|3 Minuten < d < 15 Minuten|15 Minuten < d < 30 Minuten| 30 Minuten < d
Dauer der Miniaturansicht|15 Sekunden (2 bis 3 Szenen)| 30 Sekunden (3 bis 5 Szenen)|60 Sekunden (5 bis 10 Szenen)|90 Sekunden (10 bis 15 Szenen)


Die folgende JSON legt verfügbare Parameter fest.
	
	{
	    "version": "1.0",
	    "options": {
	        "outputAudio": "true",
	        "maxMotionThumbnailDurationInSecs": "10",
	        "fadeInFadeOut": "true"
	    }
	}

## Beispielcode

Das folgende Programm zeigt Ihnen, wie Sie folgendes ausführen:

1. Sie ein Asset erstellen und eine Mediendatei in das Asset hochladen.
1. Erstellt einen Auftrag mit einer Video-Miniaturansicht anhand einer Konfigurationsdatei, die die folgende JSON-Voreinstellung enthält. 
		
		{				
			"version": "1.0",
		    "options": {
		        "outputAudio": "true",
	    	    "maxMotionThumbnailDurationInSecs": "30",
	    	    "fadeInFadeOut": "false"
		    }
		}

1. Sie die Ausgabedateien herunterladen.

###.NET code
	 
    using System;
	using System.Configuration;
	using System.IO;
	using System.Linq;
	using Microsoft.WindowsAzure.MediaServices.Client;
	using System.Threading;
	using System.Threading.Tasks;
	
	namespace VideoSummarization
	{
	    class Program
	    {
	        // Read values from the App.config file.
	        private static readonly string _mediaServicesAccountName =
	            ConfigurationManager.AppSettings["MediaServicesAccountName"];
	        private static readonly string _mediaServicesAccountKey =
	            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	
	        // Field for service context.
	        private static CloudMediaContext _context = null;
	        private static MediaServicesCredentials _cachedCredentials = null;
	
	        static void Main(string[] args)
	        {
	
	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            _mediaServicesAccountName,
	                            _mediaServicesAccountKey);
	            // Used the cached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);
	

	            // Run the thumbnail job.
	            var asset = RunVideoThumbnailJob(@"C:\supportFiles\VideoThumbnail\BigBuckBunny.mp4",
	                                        @"C:\supportFiles\VideoThumbnail\config.json");
	
	            // Download the job output asset.
	            DownloadAsset(asset, @"C:\supportFiles\VideoThumbnail\Output");
	        }
	
	        static IAsset RunVideoThumbnailJob(string inputMediaFilePath, string configurationFile)
	        {
	            // Create an asset and upload the input media file to storage.
	            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
	                "My Video Thumbnail Input Asset",
	                AssetCreationOptions.None);
	
	            // Declare a new job.
	            IJob job = _context.Jobs.Create("My Video Thumbnail Job");
	
	            // Get a reference to Azure Media Video Thumbnails.
	            string MediaProcessorName = "Azure Media Video Thumbnails";
	
	            var processor = GetLatestMediaProcessorByName(MediaProcessorName);
	
	            // Read configuration from the specified file.
	            string configuration = File.ReadAllText(configurationFile);
	
	            // Create a task with the encoding details, using a string preset.
	            ITask task = job.Tasks.AddNew("My Video Thumbnail Task",
	                processor,
	                configuration,
	                TaskOptions.None);
	
	            // Specify the input asset.
	            task.InputAssets.Add(asset);
	
	            // Add an output asset to contain the results of the job.
	            task.OutputAssets.AddNew("My Video Thumbnail Output Asset", AssetCreationOptions.None);
	
	            // Use the following event handler to check job progress.  
	            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
	
	            // Launch the job.
	            job.Submit();
	
	            // Check job execution and wait for job to finish.
	            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
	
	            progressJobTask.Wait();
	
	            // If job state is Error, the event handling
	            // method for job progress should log errors.  Here we check
	            // for error state and exit if needed.
	            if (job.State == JobState.Error)
	            {
	                ErrorDetail error = job.Tasks.First().ErrorDetails.First();
	                Console.WriteLine(string.Format("Error: {0}. {1}",
	                                                error.Code,
	                                                error.Message));
	                return null;
	            }
	
	            return job.OutputMediaAssets[0];
	        }
	
	        static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
	        {
	            IAsset asset = _context.Assets.Create(assetName, options);
	
	            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
	            assetFile.Upload(filePath);
	
	            return asset;
	        }
	
	        static void DownloadAsset(IAsset asset, string outputDirectory)
	        {
	            foreach (IAssetFile file in asset.AssetFiles)
	            {
	                file.Download(Path.Combine(outputDirectory, file.Name));
	            }
	        }
	
	        static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	        {
	            var processor = _context.MediaProcessors
	                .Where(p => p.Name == mediaProcessorName)
	                .ToList()
	                .OrderBy(p => new Version(p.Version))
	                .LastOrDefault();
	
	            if (processor == null)
	                throw new ArgumentException(string.Format("Unknown media processor",
	                                                           mediaProcessorName));
	
	            return processor;
	        }
	
	        static private void StateChanged(object sender, JobStateChangedEventArgs e)
	        {
	            Console.WriteLine("Job state changed event:");
	            Console.WriteLine("  Previous state: " + e.PreviousState);
	            Console.WriteLine("  Current state: " + e.CurrentState);
	
	            switch (e.CurrentState)
	            {
	                case JobState.Finished:
	                    Console.WriteLine();
	                    Console.WriteLine("Job is finished.");
	                    Console.WriteLine();
	                    break;
	                case JobState.Canceling:
	                case JobState.Queued:
	                case JobState.Scheduled:
	                case JobState.Processing:
	                    Console.WriteLine("Please wait...\n");
	                    break;
	                case JobState.Canceled:
	                case JobState.Error:
	                    // Cast sender as a job.
	                    IJob job = (IJob)sender;
	                    // Display or log error details as needed.
	                    // LogJobStop(job.Id);
	                    break;
	                default:
	                    break;
	            }
	        }
	
	    }
    }

###Ausgabe der Video-Miniaturansicht

[Ausgabe der Video-Miniaturansicht](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Fd06f24dc-bc81-488e-a8d0-348b7dc41b56%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Verwandte Links

[Azure Media Services Analytics – Übersicht](media-services-analytics-overview.md)

[Azure Media Analytics-Demos](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

<!---HONumber=AcomDC_0420_2016-->