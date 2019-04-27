---
title: Como fazer uma chamada telefónica através do Twilio (.NET) | Documentos da Microsoft
description: Saiba como fazer uma chamada telefónica e enviar uma mensagem SMS com o serviço de Twilio API no Azure. Exemplos de códigos escritos no .NET.
services: ''
documentationcenter: .net
author: devinrader
manager: timlt
editor: ''
ms.assetid: 789185ad-69dc-4e9e-a936-42e0a25315c8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/04/2016
ms.author: jeconnoc
ms.openlocfilehash: c41057203da949e371f62332e938feb92e84534f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60422819"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Como fazer uma chamada telefónica através do Twilio numa função da web no Azure
Este guia demonstra como utilizar o Twilio para fazer uma chamada de uma página da web alojadas no Azure. O aplicativo resultante pede ao utilizador para fazer uma chamada com o número especificado e a mensagem, conforme mostrado na captura de ecrã seguinte.

![Formulário de chamada do Azure com o Twilio e ASP.NET][twilio_dotnet_basic_form]

## <a name="twilio-prereqs"></a>Pré-requisitos
Precisará fazer o seguinte para utilizar o código neste tópico:

1. Adquirir uma conta do Twilio e autenticação de token do [Twilio consola][twilio_console]. Para começar a utilizar com o Twilio, inscreva-se em [ https://www.twilio.com/try-twilio ] [ try_twilio]. Pode avaliar a preços em [ https://www.twilio.com/pricing ] [ twilio_pricing]. Para obter informações sobre a API fornecida pelo Twilio, consulte [ https://www.twilio.com/voice/api ] [ twilio_api].
2. Adicionar a *biblioteca .NET do Twilio* para a função da web. Ver **para adicionar as bibliotecas do Twilio ao seu projeto de função da web**, mais adiante neste tópico.

Deve estar familiarizado com a criação básica [função da Web no Azure][azure_webroles_get_started].

## <a name="howtocreateform"></a>Como: Criar um formulário da web para fazer uma chamada
<a id="use_nuget"></a>Para adicionar as bibliotecas do Twilio ao seu projeto de função da web:

1. Abra a solução no Visual Studio.
2. Com o botão direito **referências**.
3. Clique em **gerir pacotes NuGet**.
4. Clique em **Online**.
5. Na caixa de pesquisa online, escreva *twilio*.
6. Clique em **instalar** no pacote do Twilio.

O código seguinte mostra como criar um formulário da web para obter dados de utilizador para fazer uma chamada. Neste exemplo, uma função da Web ASP.NET com o nome **TwilioCloud** é criado.

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

## <a id="howtocreatecode"></a>Como: Criar o código para fazer a chamada
O código a seguir, que é chamado quando o usuário preenche o formulário, cria a mensagem de chamada e gera a chamada. Neste exemplo, o código é executado no manipulador de eventos onclick do botão no formulário. (Utilize a sua conta do Twilio e autenticação de token em vez dos valores de marcador de posição atribuídos a `accountSID` e `authToken` no código abaixo.)

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

A chamada é feita, e o ponto de extremidade do Twilio, a versão de API e o estado de chamada são apresentados. Captura de ecrã seguinte mostra a saída de uma execução de exemplo.

![Resposta de chamada do Azure com o Twilio e ASP.NET][twilio_dotnet_basic_form_output]

Obter mais informações sobre TwiML podem ser encontradas em [ https://www.twilio.com/docs/api/twiml ] [ twiml]. Obter mais informações sobre &lt;Digamos&gt; e a outros verbos Twilio podem ser encontrados em [ https://www.twilio.com/docs/api/twiml/say ] [ twilio_say].

## <a id="nextsteps"></a>Passos seguintes
Esse código foi fornecido para mostrar a funcionalidade básica com o Twilio numa função da web ASP.NET no Azure. Antes de implementar para o Azure na produção, pode querer adicionar tratamento de erros mais ou outros recursos. Por exemplo:

* Em vez de usar um formulário da web, poderia usar o armazenamento de Blobs do Azure ou uma instância de base de dados do Azure SQL para armazenar números de telefone e chamar o texto. Para obter informações sobre a utilização de Blobs no Azure, consulte [como utilizar o serviço de armazenamento de Blobs do Azure no .NET][howto_blob_storage_dotnet]. Para obter informações sobre como utilizar a base de dados SQL, consulte [como utilizar a base de dados do Azure SQL em aplicações de .NET][howto_sql_azure_dotnet].
* Poderia usar `RoleEnvironment.getConfigurationSettings` para recuperar o Twilio ID da conta e a autenticação de token de definições de configuração da sua implementação, em vez de embutir no código os valores em seu formulário. Para obter informações sobre o `RoleEnvironment` classe, consulte [Microsoft.WindowsAzure.ServiceRuntime espaço de nomes][azure_runtime_ref_dotnet].
* Leia as diretrizes de segurança do Twilio em [ https://www.twilio.com/docs/security ] [ twilio_docs_security].
* Saiba mais sobre o Twilio na [ https://www.twilio.com/docs ] [ twilio_docs].

## <a name="seealso"></a>Ver também
* [Como utilizar o Twilio para funcionalidades de voz e SMS a partir do Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

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
