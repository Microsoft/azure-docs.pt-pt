---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: d71d52257b6e8cfa243207c9bfdb5c7de7d3dd37
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891048"
---
1. No arquivo de projeto MainPage.xaml.cs, adicione as seguintes **usando** instruções:
   
        using System.Linq;        
        using Windows.Security.Credentials;
2. Substitua a **AuthenticateAsync** método com o código a seguir:
   
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
   
    Nesta versão do **AuthenticateAsync**, a aplicação tenta utilizar credenciais armazenadas no **PasswordVault** para aceder ao serviço. Um regular início de sessão é também executado quando não existe nenhuma credencial armazenada.
   
   > [!NOTE]
   > Um token em cache pode ter expirado e expiração do token também pode ocorrer após a autenticação quando a aplicação está em utilização. Para saber como determinar se um token expirou, veja [verificar a existência de tokens de autenticação expirado](https://aka.ms/jww5vp). Para uma solução de tratamento de erros de autorização relacionados com tokens prestes a expirar, consulte a postagem [colocação em cache e a manipulação de tokens expirados em serviços móveis do Azure geridos SDK](https://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx). 
   > 
   > 
3. Reinicie a aplicação de duas vezes.
   
    Tenha em atenção que na primeira inicialização, inicie sessão com o fornecedor é novamente necessária. No entanto, no segundo reinício são utilizadas as credenciais em cache e início de sessão é ignorada. 

