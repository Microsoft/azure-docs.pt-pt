---
title: Como fazer uma chamada de Twilio (.NET) [ Microsoft Docs
description: Saiba como fazer uma chamada telefónica e envie uma mensagem SMS com o serviço Twilio API no Azure. Amostras de código escritas em .NET.
services: ''
documentationcenter: .net
author: mimckitt
editor: ''
ms.assetid: 789185ad-69dc-4e9e-a936-42e0a25315c8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/04/2016
ms.author: mimckitt
ms.openlocfilehash: df1f5e1c21c28fa8c1fcdef6b2278fb92014a3b1
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272564"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Como fazer uma chamada telefónica usando Twilio em um papel web em Azure
Este guia demonstra como usar twilio para fazer uma chamada a partir de uma página web hospedada em Azure. A aplicação resultante leva o utilizador a fazer uma chamada com o número e mensagem dado, como mostra a seguinte imagem.

![Formulário de chamada Azure usando Twilio e ASP.NET][twilio_dotnet_basic_form]

## <a name="prerequisites"></a><a name="twilio-prereqs"></a>Pré-requisitos
Terá de fazer o seguinte para utilizar o código neste tópico:

1. Adquira uma conta Twilio e um símbolo de autenticação da [Consola Twilio][twilio_console]. Para começar com Twilio, [https://www.twilio.com/try-twilio][try_twilio]inscreva-se em . Pode avaliar os [https://www.twilio.com/pricing][twilio_pricing]preços em . Para obter informações sobre a API [https://www.twilio.com/voice/api][twilio_api]fornecida por Twilio, consulte .
2. Adicione a *biblioteca Twilio .NET* ao seu papel web. Ver **Adicionar as bibliotecas Twilio ao seu projeto**de papel web , mais tarde neste tópico.

Deve estar familiarizado com a criação de um papel web básico [no Azure.][azure_webroles_get_started]

## <a name="how-to-create-a-web-form-for-making-a-call"></a><a name="howtocreateform"></a>Como: Criar um formulário web para fazer uma chamada
<a id="use_nuget"></a>Para adicionar as bibliotecas Twilio ao seu projeto de papel web:

1. Abra a sua solução no Estúdio Visual.
2. **Referências**do clique direito .
3. Clique em **gerir pacotes NuGet**.
4. Clique **em Online**.
5. Na caixa online de pesquisa, *digite twilio*.
6. Clique em **Instalar** no pacote Twilio.

O código que se segue mostra como criar um formulário web para recuperar os dados dos utilizadores para fazer uma chamada. Neste exemplo, é criado um ASP.NET Web Role chamado **TwilioCloud.**

```aspx
<%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
    AutoEventWireup="true" CodeBehind="Default.aspx.cs"
    Inherits="WebRole1._Default" %>

<asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
</asp:Content>
<asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
    <div>
        <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
        </asp:BulletedList>
    </div>
    <div>
        <p>Fill in all fields and click <b>Make this call</b>.</p>
        <div>
            To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
            Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
            <asp:Button ID="callpage" runat="server" Text="Make this call"
                onclick="callpage_Click" />
        </div>
    </div>
</asp:Content>
```

## <a name="how-to-create-the-code-to-make-the-call"></a><a id="howtocreatecode"></a>Como: Criar o código para fazer a chamada
O seguinte código, que é chamado quando o utilizador completa o formulário, cria a mensagem de chamada e gera a chamada. Neste exemplo, o código é executado no manipulador de eventos onclick do botão no formulário. (Utilize a sua conta Twilio e o símbolo de `accountSID` autenticação em vez dos valores reservados atribuídos e `authToken` no código abaixo.)

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using Twilio;
using Twilio.Http;
using Twilio.Types;
using Twilio.Rest.Api.V2010;

namespace WebRole1
{
    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {

        }

        protected void callpage_Click(object sender, EventArgs e)
        {
            // Call processing happens here.

            // Use your account SID and authentication token instead of
            // the placeholders shown here.
            var accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
            var authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

            // Instantiate an instance of the Twilio client.
            TwilioClient.Init(accountSID, authToken);

            // Retrieve the account, used later to retrieve the
            var account = AccountResource.Fetch(accountSID);

            this.varDisplay.Items.Clear();

            if (this.toNumber.Text == "" || this.message.Text == "")
            {
                this.varDisplay.Items.Add(
                        "You must enter a phone number and a message.");
            }
            else
            {
                // Retrieve the values entered by the user.
                var to = PhoneNumber(this.toNumber.Text);
                var from = new PhoneNumber("+14155992671");
                var myMessage = this.message.Text;

                // Create a URL using the Twilio message and the user-entered
                // text. You must replace spaces in the user's text with '%20'
                // to make the text suitable for a URL.
                var url = $"https://twimlets.com/message?Message%5B0%5D={myMessage.Replace(" ", "%20")}";
                var twimlUri = new Uri(url);

                // Display the endpoint, API version, and the URL for the message.
                this.varDisplay.Items.Add($"Using Twilio endpoint {
                }");
                this.varDisplay.Items.Add($"Twilioclient API Version is {apiVersion}");
                this.varDisplay.Items.Add($"The URL is {url}");

                // Place the call.
                var call = CallResource.create(to, from, url: twimlUri);
                this.varDisplay.Items.Add("Call status: " + call.Status);
            }
        }
    }
}
```

A chamada é feita e o ponto final de Twilio, a versão API e o estado da chamada são apresentados. A imagem que se segue mostra a saída de uma amostra.

![Resposta de chamada azure usando Twilio e ASP.NET][twilio_dotnet_basic_form_output]

Mais informações sobre o TwiML podem ser encontradas em [https://www.twilio.com/docs/api/twiml][twiml]. Mais informações sobre &lt;Say&gt; e outros verbos [https://www.twilio.com/docs/api/twiml/say][twilio_say]Twilio podem ser encontrados em .

## <a name="next-steps"></a><a id="nextsteps"></a>Passos seguintes
Este código foi fornecido para mostrar-lhe a funcionalidade básica usando Twilio numa ASP.NET papel web no Azure. Antes de ser implantado para o Azure em produção, é melhor adicionar mais manipulação de erros ou outras funcionalidades. Por exemplo:

* Em vez de utilizar um formulário web, pode utilizar o armazenamento Azure Blob ou uma instância de Base de Dados Azure SQL para armazenar números de telefone e texto de chamada. Para obter informações sobre a utilização de Blobs em Azure, consulte Como utilizar o serviço de [armazenamento Azure Blob em .NET][howto_blob_storage_dotnet]. Para obter informações sobre a utilização da Base de Dados SQL, consulte como utilizar a Base de [Dados Azure SQL em aplicações .NET][howto_sql_azure_dotnet].
* Pode utilizar `RoleEnvironment.getConfigurationSettings` para recuperar o ID da conta Twilio e o símbolo de autenticação das definições de configuração da sua implementação, em vez de codificar duramente os valores na sua forma. Para obter `RoleEnvironment` informações sobre a classe, consulte [Microsoft.WindowsAzure.ServiceRuntime Namespace][azure_runtime_ref_dotnet].
* Leia as Diretrizes de [https://www.twilio.com/docs/security][twilio_docs_security]Segurança twilio em .
* Saiba mais sobre Twilio em [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a><a name="seealso"></a>Consulte também
* [Como usar twilio para capacidades de Voz e SMS do Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: https://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
[azure_webroles_get_started]: https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-get-started
