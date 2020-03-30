---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: d71d52257b6e8cfa243207c9bfdb5c7de7d3dd37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184940"
---
1. No ficheiro MainPage.xaml.cs projeto, adicione o seguinte **utilizando** declarações:
   
        using System.Linq;        
        using Windows.Security.Credentials;
2. Substitua o método **AuthenticateAsync** pelo seguinte código:
   
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
   
            // This sample uses the Facebook provider.
            var provider = MobileServiceAuthenticationProvider.Facebook;
   
            // Use the PasswordVault to securely store and access credentials.
            PasswordVault vault = new PasswordVault();
            PasswordCredential credential = null;
   
            try
            {
                // Try to get an existing credential from the vault.
                credential = vault.FindAllByResource(provider.ToString()).FirstOrDefault();
            }
            catch (Exception)
            {
                // When there is no matching resource an error occurs, which we ignore.
            }
   
            if (credential != null)
            {
                // Create a user from the stored credentials.
                user = new MobileServiceUser(credential.UserName);
                credential.RetrievePassword();
                user.MobileServiceAuthenticationToken = credential.Password;
   
                // Set the user from the stored credentials.
                App.MobileService.CurrentUser = user;
   
                // Consider adding a check to determine if the token is 
                // expired, as shown in this post: https://aka.ms/jww5vp.
   
                success = true;
                message = string.Format("Cached credentials for user - {0}", user.UserId);
            }
            else
            {
                try
                {
                    // Sign in with the identity provider.
                    user = await App.MobileService
                        .LoginAsync(provider, "{url_scheme_of_your_app}");
   
                    // Create and store the user credentials.
                    credential = new PasswordCredential(provider.ToString(),
                        user.UserId, user.MobileServiceAuthenticationToken);
                    vault.Add(credential);
   
                    success = true;
                    message = string.Format("You are now signed in - {0}", user.UserId);
                }
                catch (MobileServiceInvalidOperationException)
                {
                    message = "You must sign in. Sign-In Required";
                }
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
   
            return success;
        }
   
    Nesta versão do **AuthenticateAsync,** a aplicação tenta utilizar credenciais armazenadas no **PasswordVault** para aceder ao serviço. É também realizado um registo regular quando não há credencial armazenada.
   
   > [!NOTE]
   > Um token em cache pode ser expirado, e a expiração do token também pode ocorrer após a autenticação quando a aplicação está em uso. Para saber determinar se um token está expirado, consulte Verifique se há fichas de [autenticação expiradas](https://aka.ms/jww5vp). Para obter uma solução para lidar com erros de autorização relacionados com fichas de caducidade, consulte os [tokens expirados do post Caching e do manuseamento em Serviços Móveis Azure geridos pela SDK](https://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx). 
   > 
   > 
3. Reinicie a aplicação duas vezes.
   
    Note que na primeira start-up, é novamente necessário iniciar o início de sessão com o prestador. No entanto, no segundo reinício, as credenciais em cache são utilizadas e o início de inscrição é ignorado. 

