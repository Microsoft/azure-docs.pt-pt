---
title: Azure MFA software development kit para aplicações personalizadas - Azure Active Directory
description: Este artigo mostra-lhe como transferir e utilizar o SDK de MFA do Azure para ativar a verificação de dois passos para as suas aplicações personalizadas.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b6f5def70dcb2564e92c04e53b5d2ef5f0631fd
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310868"
---
# <a name="building-multi-factor-authentication-into-custom-apps-sdk"></a>Criando o multi-factor Authentication nas aplicações personalizadas (SDK)

> [!IMPORTANT]
> Tem sido anunciada a descontinuação do Azure Multi-Factor Authentication Software Development Kit (SDK). Esta funcionalidade já não ser suportada para os novos clientes. Os clientes atuais podem continuar a utilizar o SDK até 14 de novembro de 2018. Após esse tempo, as chamadas para o SDK irão falhar. 

O Azure multi-factor Authentication Software Development Kit (SDK) permitem-lhe criar a verificação de dois passos diretamente para os processos de início de sessão ou a transação de aplicativos no seu inquilino do Azure AD.

O SDK do multi-factor Authentication está disponível para c#, Visual Basic (.NET), Java, Perl, PHP e Ruby. O SDK fornece um wrapper estreito em torno de verificação de dois passos. Inclui tudo o que precisa para escrever seu código, incluindo arquivos de código de origem comentado, arquivos de exemplo e um arquivo Leiame detalhado. Cada SDK também inclui um certificado e chave privada para encriptar as transações que são exclusivas para o seu fornecedor do multi-factor Authentication. Desde que tenham um fornecedor, pode transferir o SDK em tantas linguagens e formatos conforme necessário.

A estrutura das APIs no SDK do multi-factor Authentication é simple. Fazer uma única função de chamada para uma API com os parâmetros de opção de multi-factor (como o modo de verificação) e os dados de utilizador (como o número de telefone para chamar ou o número PIN para validar). As APIs de traduzir a chamada de função em pedidos de serviços da web para o serviço baseado na nuvem do Azure multi-factor Authentication. Todas as chamadas tem de incluir uma referência ao certificado privada que está incluída em cada SDK.

Uma vez que as APIs não tiver acesso a utilizadores registados no Azure Active Directory, tem de fornecer informações do usuário num arquivo ou banco de dados. Além disso, as APIs não fornecem funcionalidades de gestão de inscrição ou utilizador, por isso terá de criar esses processos na sua aplicação.

> [!IMPORTANT]
> Para transferir o SDK, tem de criar um Fornecedor do Multi-Factor Auth do Azure, mesmo que tenha licenças MFA do Azure, AAD Premium ou EMS. Se criar um fornecedor do multi-factor Auth do Azure para esta finalidade e já tiver licenças, certifique-se criar o fornecedor com o **por utilizador ativado** modelo. Em seguida, associe o Fornecedor ao diretório que contém o MFA do Azure, o Azure AD Premium ou as licenças EMS. Esta configuração garante que apenas são faturadas se tiver mais utilizadores exclusivos com o SDK do que o número de licenças que possui.


## <a name="download-the-sdk"></a>Transferir o SDK
Baixar o SDK do multi-factor do Azure requer uma [Azure multi-factor Auth Provider](concept-mfa-authprovider.md).  Isto requer uma subscrição do Azure completo, mesmo que são propriedade licenças de MFA do Azure, Azure AD Premium ou Enterprise Mobility Suite. Os métodos públicos de baixar o SDK tem sido encerrados, uma vez que o SDK foi preterido. Deve abrir um incidente de suporte com a Microsoft se precisar de transferir o SDK. O SDK é fornecido apenas para clientes que já estão a utilizar o SDK. Novos clientes não será carregada.

## <a name="whats-in-the-sdk"></a>O que está no SDK
O SDK inclui os seguintes itens:

* **README**. Explica como utilizar as APIs de autenticação do multi-factor de uma aplicação nova ou existente.
* **Arquivos de origem** para multi-factor Authentication
* **Certificado de cliente** que utilizar para comunicar com o serviço de multi-factor Authentication
* **Chave privada** para o certificado
* **Chame os resultados.** Uma lista de códigos de resultado de chamada. Para abrir este ficheiro, utilize uma aplicação com formatação de texto, como o WordPad. Utilize os códigos de resultado de chamada para testar e resolver problemas relacionados com a implementação de multi-factor Authentication no seu aplicativo. Não são os códigos de estado de autenticação.
* **Exemplos.** Código de exemplo para uma implementação básica de trabalho de multi-factor Authentication.

> [!WARNING]
> O certificado de cliente é um certificado privado exclusivo que foi gerado especialmente para. Não partilhe ou perder este ficheiro. É a chave para garantir a segurança das suas comunicações com o serviço de multi-factor Authentication.

## <a name="code-sample"></a>Exemplo de código
Este exemplo de código mostra como utilizar as APIs de SDK do multi-factor Authentication para adicionar a verificação de chamada de voz de modo padrão para a sua aplicação. O modo padrão é uma chamada telefónica de que o usuário responda ao premir a tecla #.

Este exemplo utiliza o c# 2.0 multi-factor Authentication SDK do .NET num aplicativo ASP.NET básico com a lógica de lado do servidor do c#, mas o processo é semelhante em outras linguagens. Uma vez que o SDK inclui ficheiros de origem, arquivos não executáveis, pode criar os ficheiros e referenciá-los ou incluí-los diretamente na sua aplicação.

> [!NOTE]
> Ao implementar o multi-factor Authentication, utilize os métodos adicionais (chamada telefónica ou mensagem de texto) como secundária ou terciária verificação para complementar o seu método de autenticação primária (nome de utilizador e palavra-passe). Esses métodos não são projetados como métodos de autenticação principal.

### <a name="code-sample-overview"></a>Descrição geral do exemplo de código
Este código de exemplo para um aplicativo de demonstração simples web utiliza uma chamada telefónica com uma resposta de chave de # para verificar a autenticação do utilizador. Esse fator de uma chamada telefónica é conhecido no multi-factor Authentication como modo padrão.

O código do lado do cliente não inclui quaisquer elementos específicos do multi-factor Authentication. Uma vez que os fatores de autenticação adicionais são independentes da autenticação primária, pode adicioná-los sem alterar a interface de início de sessão existente. As APIs no SDK do multi-factor permitem-lhe personalizar a experiência do usuário, mas não poderá ter de alterar alguma informação de todo.

O código do lado do servidor adiciona modo padrão de autenticação no passo 2. Ele cria um objeto de PfAuthParams com os parâmetros que são necessários para o modo padrão de verificação: nome de utilizador, por telefone e modo, juntamente com o caminho para o certificado de cliente (CertFilePath), que é necessário em cada chamada. Para uma demonstração de todos os parâmetros no PfAuthParams, consulte o ficheiro de exemplo no SDK.

Em seguida, o código passa o objeto de PfAuthParams para a função de pf_authenticate(). O valor de retorno indica o êxito ou falha da autenticação. Os parâmetros, callStatus e errorID, contêm informações de resultado de chamada adicionais. Os códigos de resultado de chamada são documentados no ficheiro de resultados de chamada no SDK.

Essa implementação mínima pode ser escrita em algumas linhas. No entanto, no código de produção, incluiria o tratamento de erros mais sofisticado, código de base de dados adicionais e experiência do usuário.

### <a name="web-client-code"></a>Código de cliente da Web
Segue-se código de cliente da web para uma página de demonstração.

    <%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="\_Default" %>

    <!DOCTYPE html>

    <html xmlns="https://www.w3.org/1999/xhtml">
    <head runat="server">
    <title>Multi-Factor Authentication Demo</title>
    </head>
    <body>
    <h1>Azure Multi-Factor Authentication Demo</h1>
    <form id="form1" runat="server">

    <div style="width:auto; float:left">
    Username:&nbsp;<br />
    Password:&nbsp;<br />
    </div>

    <div">
    <asp:TextBox id="username" runat="server" width="100px"/><br />
    <asp:Textbox id="password" runat="server" width="100px" TextMode="password" /><br />
    </div>

    <asp:Button id="btnSubmit" runat="server" Text="Log in" onClick="btnSubmit_Click"/>

    <p><asp:Label ID="lblResult" runat="server"></asp:Label></p>

    </form>
    </body>
    </html>


### <a name="server-side-code"></a>Código do Lado do Servidor
No seguinte código do lado do servidor, o multi-factor Authentication está configurado e execute no passo 2. Modo padrão (MODE_STANDARD) é uma chamada telefónica para o qual o usuário responda ao premir a tecla #.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;

    public partial class \_Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {
        }

        protected void btnSubmit_Click(object sender, EventArgs e)
        {
            // Step 1: Validate the username and password
            if (username.Text != "Contoso" || password.Text != "password")
            {
                lblResult.ForeColor = System.Drawing.Color.Red;
                lblResult.Text = "Username or password incorrect.";
            }
            else
            {
                // Step 2: Perform multi-factor authentication

                // Add call details from the user database.
                PfAuthParams pfAuthParams = new PfAuthParams();
                pfAuthParams.Username = username.Text;
                pfAuthParams.Phone = "5555555555";
                pfAuthParams.Mode = pf_auth.MODE_STANDARD;

                // Specify a client certificate
                // NOTE: This file contains the private key for the client
                // certificate. It must be stored with appropriate file
                // permissions.
                pfAuthParams.CertFilePath = "c:\\cert_key.p12";

                // Perform phone-based authentication
                int callStatus;
                int errorId;

                if(pf_auth.pf_authenticate(pfAuthParams, out callStatus, out errorId))
                {
                    lblResult.ForeColor = System.Drawing.Color.Green;
                    lblResult.Text = "Multi-Factor Authentication succeeded.";
                }
                else
                {
                    lblResult.ForeColor = System.Drawing.Color.Red;
                    lblResult.Text = "Multi-Factor Authentication failed.";
                }
            }

        }
    }
