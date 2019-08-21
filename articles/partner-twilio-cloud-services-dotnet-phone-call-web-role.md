---
title: Como fazer uma chamada telefônica do twilio (.NET) | Microsoft Docs
description: Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de API do twilio no Azure. Exemplos de código escritos em .NET.
services: ''
documentationcenter: .net
author: georgewallace
editor: ''
ms.assetid: 789185ad-69dc-4e9e-a936-42e0a25315c8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/04/2016
ms.author: gwallace
ms.openlocfilehash: 27b4f3cdd8f622a97cfc0853f79bb77d76673dcf
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69636145"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Como fazer uma chamada telefônica usando o twilio em uma função Web no Azure
Este guia demonstra como usar o twilio para fazer uma chamada de uma página da web hospedada no Azure. O aplicativo resultante solicita que o usuário faça uma chamada com o número e a mensagem fornecidos, conforme mostrado na captura de tela a seguir.

![Formulário de chamada do Azure usando twilio e ASP.NET][twilio_dotnet_basic_form]

## <a name="twilio-prereqs"></a>Pré-requisitos
Você precisará fazer o seguinte para usar o código neste tópico:

1. Adquira uma conta do twilio e um token de autenticação do [console do twilio][twilio_console]. Para começar a usar o twilio, inscreva [https://www.twilio.com/try-twilio][try_twilio]-se em. Você pode avaliar os preços [https://www.twilio.com/pricing][twilio_pricing]em. Para obter informações sobre a API fornecida pelo twilio, [https://www.twilio.com/voice/api][twilio_api]consulte.
2. Adicione a *biblioteca do .net do twilio* à sua função Web. Consulte **para adicionar as bibliotecas twilio ao seu projeto de função Web**, posteriormente neste tópico.

Você deve estar familiarizado com a criação de uma [função Web básica no Azure][azure_webroles_get_started].

## <a name="howtocreateform"></a>Como: Criar um formulário da Web para fazer uma chamada
<a id="use_nuget"></a>Para adicionar as bibliotecas twilio ao seu projeto de função Web:

1. Abra sua solução no Visual Studio.
2. Clique com o botão direito do mouse em **referências**.
3. Clique em **gerenciar pacotes NuGet**.
4. Clique em **online**.
5. Na caixa Pesquisar online, digite *twilio*.
6. Clique em **instalar** no pacote twilio.

O código a seguir mostra como criar um formulário da Web para recuperar dados de usuário para fazer uma chamada. Neste exemplo, uma função Web ASP.NET chamada **TwilioCloud** é criada.

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
O código a seguir, que é chamado quando o usuário conclui o formulário, cria a mensagem de chamada e gera a chamada. Neste exemplo, o código é executado no manipulador de eventos onclick do botão no formulário. (Use sua conta do twilio e o token de autenticação em vez dos valores `accountSID` de `authToken` espaço reservado atribuídos a e no código a seguir.)

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

A chamada é feita, e o ponto de extremidade twilio, a versão da API e o status da chamada são exibidos. A captura de tela a seguir mostra a saída de uma execução de exemplo.

![Resposta de chamada do Azure usando twilio e ASP.NET][twilio_dotnet_basic_form_output]

Mais informações sobre TwiML podem ser encontradas em [https://www.twilio.com/docs/api/twiml][twiml]. Mais informações sobre &lt;digamos&gt; e outros verbos do twilio podem ser encontradas em [https://www.twilio.com/docs/api/twiml/say][twilio_say].

## <a id="nextsteps"></a>Passos seguintes
Esse código foi fornecido para mostrar a funcionalidade básica usando twilio em uma função Web ASP.NET no Azure. Antes de implantar no Azure em produção, convém adicionar mais tratamento de erro ou outros recursos. Por exemplo:

* Em vez de usar um formulário da Web, você pode usar o armazenamento de BLOBs do Azure ou uma instância do banco de dados SQL do Azure para armazenar números de telefone e texto de chamada. Para obter informações sobre como usar BLOBs no Azure, consulte [como usar o serviço de armazenamento de BLOBs do Azure no .net][howto_blob_storage_dotnet]. Para obter informações sobre como usar o banco de dados SQL, consulte [como usar o banco de dados SQL do Azure em aplicativos .net][howto_sql_azure_dotnet].
* Você pode usar `RoleEnvironment.getConfigurationSettings` para recuperar a ID da conta do twilio e o token de autenticação das definições de configuração da implantação, em vez de embutir os valores no seu formulário. Para obter informações sobre `RoleEnvironment` a classe, consulte [namespace Microsoft. WindowsAzure. onruntime][azure_runtime_ref_dotnet].
* Leia as diretrizes de segurança do [https://www.twilio.com/docs/security][twilio_docs_security]twilio em.
* Saiba mais sobre o twilio [https://www.twilio.com/docs][twilio_docs]em.

## <a name="seealso"></a>Ver também
* [Como usar o twilio para recursos de voz e SMS do Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

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
