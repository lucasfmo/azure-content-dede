<properties
	pageTitle="Konfigurieren der Microsoft-Kontoauthentifizierung für Ihre App Services-Anwendung"
	description="Erfahren Sie, wie Sie die Microsoft-Kontoauthentifizierung für Ihre App Services-Anwendung konfigurieren."
	authors="mattchenderson"
	services="app-service"
	documentationCenter=""
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahender"/>

# Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Microsoft-Kontoanmeldung

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In diesem Thema wird demonstriert, wie Sie Azure App Services zur Verwendung eines Microsoft-Kontos als Authentifizierungsanbieter konfigurieren.

## <a name="register-microsoft-account"> </a>Registrieren Ihrer App mit einem Microsoft-Konto

1. Melden Sie sich beim [Azure-Portal] an, und navigieren Sie zu Ihrer Anwendung. Kopieren Sie die **URL**. Sie verwenden diese URL, um Ihre App mit einem Microsoft-Konto zu konfigurieren.

2. Navigieren Sie im Microsoft Account Developer Center zur Seite [Eigene Anwendungen], und melden Sie sich, falls erforderlich, mit Ihrem Microsoft-Konto an.

3. Klicken Sie auf **App hinzufügen**, geben Sie einen Anwendungsnamen ein, und klicken Sie auf **Anwendung erstellen**.

4. Notieren Sie sich die **Anwendungs-ID**, da Sie sie später benötigen.

5. Klicken Sie unter „Plattformen“ auf **Plattform hinzufügen**, und wählen Sie „Web“ aus.

6. Geben Sie unter „Umleitungs-URIs“ den Endpunkt der Anwendung an, und klicken Sie dann auf **Speichern**.
 
	>[AZURE.NOTE]Der Umleitungs-URI ist die URL Ihrer Anwendung mit angefügtem Pfad _/.auth/login/microsoftaccount/callback_. Beispiel: `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. Stellen Sie sicher, dass Sie das HTTPS-Schema verwenden.

7. Klicken Sie unter „Anwendungsgeheimnisse“ auf **Neues Kennwort generieren**. Notieren Sie sich den angezeigten Wert. Er wird nicht wieder angezeigt, nachdem Sie die Seite verlassen haben.


    > [AZURE.IMPORTANT] Bei dem Kennwort handelt es sich um eine wichtige Information für die Anmeldung. Teilen Sie das Kennwort keiner anderen Person mit, und geben Sie es nicht in einer Clientanwendung weiter.

## <a name="secrets"> </a>Hinzufügen von Microsoft-Kontoinformationen zu Ihrer App Service-Anwendung

1. Navigieren Sie im [Azure-Portal] zu Ihrer Anwendung, und klicken Sie auf **Einstellungen** > **Authentifizierung/Autorisierung**.

2. Falls das Authentifizierungs-/Autorisierungsfeature nicht aktiviert ist, legen Sie es auf **Ein** fest.

3. Klicken Sie auf **Microsoft-Konto** Fügen Sie die Werte für die Anwendungs-ID und das Kennwort ein, die Sie zuvor erhalten haben, und aktivieren Sie optional alle Bereiche, die bei Ihrer Anwendung erforderlich sind. Klicken Sie dann auf **OK**.

    ![][1]

	Standardmäßig erfolgt die Authentifizierung über App Service, wobei jedoch der Zugriff auf die Inhalte Ihrer Website und APIs nicht autorisiert wird. Sie müssen die Benutzer in Ihrem App-Code autorisieren.

4. (Optional:) Um den Zugriff auf Ihre Website ausschließlich auf Benutzer zu beschränken, die von Microsoft authentifiziert wurden, legen Sie **Action to take when request is not authenticated** auf **Microsoft Account** fest. Dadurch müssen alle Anforderungen authentifiziert werden. Alle nicht authentifizierten Anforderungen werden zur Authentifizierung an Microsoft umgeleitet.

5. Klicken Sie auf **Speichern**.

Sie können nun ein Microsoft-Konto für die Authentifizierung in Ihrer App verwenden.

## <a name="related-content"> </a>Verwandte Inhalte

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Eigene Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure-Portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0511_2016-->