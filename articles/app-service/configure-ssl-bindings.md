---
title: Proteja um DNS personalizado com uma ligação SSL
description: Secure HTTPS acesso ao seu domínio personalizado criando uma ligação TLS/SSL com um certificado. Melhore a segurança do seu website aplicando HTTPS ou TLS 1.2.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 10/25/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 263b4e76d334aab82f6bbac9aa268a50f4dd3784
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373927"
---
# <a name="secure-a-custom-dns-name-with-an-ssl-binding-in-azure-app-service"></a>Proteja um nome DNS personalizado com uma ligação SSL no Serviço de Aplicações Azure

Este artigo mostra-lhe como proteger o [domínio personalizado](app-service-web-tutorial-custom-domain.md) na sua app de serviço de aplicação ou [aplicação de função](https://docs.microsoft.com/azure/azure-functions/) do [App Service,](https://docs.microsoft.com/azure/app-service/) criando um certificado vinculativo. Quando terminar, pode aceder à sua aplicação App Service no ponto final `https://` para o seu nome DNS personalizado (por exemplo, `https://www.contoso.com`). 

![Aplicação Web com certificado SSL personalizado](./media/configure-ssl-bindings/app-with-custom-ssl.png)

A segurança de um [domínio personalizado](app-service-web-tutorial-custom-domain.md) com um certificado envolve dois passos:

- [Adicione um certificado privado ao Serviço de Aplicações](configure-ssl-certificate.md) que satisfaça todos os [requisitos para encadernações SSL](configure-ssl-certificate.md#private-certificate-requirements).
-  Crie uma ligação SSL ao domínio personalizado correspondente. Este segundo passo é coberto por este artigo.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Atualizar o escalão de preço da sua aplicação
> * Proteja um domínio personalizado com um certificado
> * Impor HTTPS
> * Impor TLS 1.1/1.2
> * Automatizar a gestão de TLS com scripts

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia:

- [Criar uma aplicação do Serviço de Aplicações](/azure/app-service/)
- [Mapeie um nome](app-service-web-tutorial-custom-domain.md) de domínio para a sua app ou [compre e configure-o em Azure](manage-custom-dns-buy-domain.md)
- [Adicione um certificado privado à sua aplicação](configure-ssl-certificate.md)

> [!NOTE]
> A maneira mais fácil de adicionar um certificado privado é criar um Certificado Gerido por Serviço de [Aplicações gratuito](configure-ssl-certificate.md#create-a-free-certificate-preview) (Pré-visualização).

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Proteja um domínio personalizado

Faça os seguintes passos:

No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>a partir do menu esquerdo, selecione **App Services** >  **\<app-name>** .

A partir da navegação à esquerda da sua aplicação, inicie o diálogo **de ligação TLS/SSL** por:

- Selecionando **domínios personalizados** > **Adicionar ligação**
- Selecionando **as definições TLS/SSL** > **adicionar ligação TLS/SSL**

![Adicionar ligação ao domínio](./media/configure-ssl-bindings/secure-domain-launch.png)

No **Domínio Personalizado,** selecione o domínio personalizado para o que pretende adicionar uma ligação.

Se a sua aplicação já tiver um certificado para o domínio personalizado selecionado, vá diretamente à [Create Binding.](#create-binding) Caso contrário, continua.

### <a name="add-a-certificate-for-custom-domain"></a>Adicione um certificado para domínio personalizado

Se a sua aplicação não tiver certificado para o domínio personalizado selecionado, então tem duas opções:

- **Carregar Certificado PFX** - Siga o fluxo de trabalho no [Upload de um certificado privado](configure-ssl-certificate.md#upload-a-private-certificate)e, em seguida, selecione esta opção aqui.
- Certificado de serviço de **aplicações de importação** - Siga o fluxo de trabalho na Importação de um certificado de Serviço de [Aplicações](configure-ssl-certificate.md#import-an-app-service-certificate)e, em seguida, selecione esta opção aqui.

> [!NOTE]
> Também pode [criar um certificado gratuito](configure-ssl-certificate.md#create-a-free-certificate-preview) (Pré-visualização) ou importar um certificado de cofre de [chave,](configure-ssl-certificate.md#import-a-certificate-from-key-vault)mas deve fazê-lo separadamente e depois voltar ao diálogo **de ligação TLS/SSL.**

### <a name="create-binding"></a>Criar encadernação

Utilize a tabela seguinte para o ajudar a configurar a ligação SSL no diálogo de **ligação TLS/SSL** e, em seguida, clique em **Adicionar Encadernação**.

| Definição | Descrição |
|-|-|
| Domínio personalizado | O nome de domínio para adicionar a ligação SSL para. |
| Impressão digital de certificado privado | O certificado para ligar. |
| Tipo TLS/SSL | <ul><li>Podem ser adicionadas ligações **[SNI SSL](https://en.wikipedia.org/wiki/Server_Name_Indication)** - Múltiplas ligações SNI SSL. Esta opção permite utilizar vários certificados SSL para proteger múltiplos domínios no mesmo endereço IP. A maioria dos navegadores modernos (incluindo Internet Explorer, Chrome, Firefox e Opera) suportam SNI (para mais informações, consulte [Indicação de Nome](https://wikipedia.org/wiki/Server_Name_Indication)do Servidor).</li><li>**IP SSL** - Só pode ser adicionada uma ligação IP SSL. Esta opção permite utilizar apenas um certificado SSL para proteger um endereço IP público dedicado. Depois de configurar a ligação, siga os passos no [Remap Um registo para IP SSL](#remap-a-record-for-ip-ssl).<br/>Ip SSL é suportado apenas em camadas de Produção ou Isolados. </li></ul> |

Uma vez concluída a operação, o estado SSL do domínio personalizado é alterado para **Secure**.

![Ligação SSL bem sucedida](./media/configure-ssl-bindings/secure-domain-finished.png)

> [!NOTE]
> Um estado **seguro** nos **domínios Personalizados** significa que está protegido com um certificado, mas o Serviço de Aplicações não verifica se o certificado é auto-assinado ou expirado, por exemplo, o que também pode fazer com que os navegadores mostrem um erro ou aviso.

## <a name="remap-a-record-for-ip-ssl"></a>Remapear um registo A para SSL IP

Se não utilizar o IP SSL na sua aplicação, salte para [o Teste HTTPS para o seu domínio personalizado](#test-https).

Por padrão, a sua aplicação utiliza um endereço IP público partilhado. Quando liga um certificado com IP SSL, o App Service cria um novo endereço IP dedicado para a sua aplicação.

Se mapeou um registo A para a sua aplicação, atualize o seu registo de domínio com este novo endereço IP dedicado.

A página de **domínio Personalizado** da sua aplicação é atualizada com o novo endereço IP dedicado. [Copie este endereço IP](app-service-web-tutorial-custom-domain.md#info) e [remapeie o registo A ](app-service-web-tutorial-custom-domain.md#map-an-a-record) para este endereço IP novo.

## <a name="test-https"></a>Tester HTTPS

Em vários navegadores, navegue para `https://<your.custom.domain>` para verificar se serve a sua aplicação.

![Navegação do portal para a aplicação do Azure](./media/configure-ssl-bindings/app-with-custom-ssl.png)

O seu código de aplicação pode inspecionar o protocolo através do cabeçalho "x-appservice-proto". O cabeçalho terá um valor de `http` ou `https`. 

> [!NOTE]
> Se a sua aplicação lhe der erros de validação de certificados, provavelmente está a usar um certificado auto-assinado.
>
> Se não for esse o caso, poderá ter deixou de fora certificados intermédios quando exportou o certificado para o ficheiro PFX.

## <a name="prevent-ip-changes"></a>Prevenir alterações ip

O seu endereço IP de entrada pode alterar-se quando elimina uma ligação, mesmo que essa ligação seja IP SSL. Isto é especialmente importante quando renova um certificado que já está numa ligação IP SSL. Para evitar uma alteração de endereço IP da sua aplicação, siga estes passos por ordem:

1. Carregar o novo certificado.
2. Vincular o novo certificado com o domínio personalizado que pretende sem eliminar os antigo. Esta ação substitui o enlace em vez de remover o antigo.
3. Eliminar o certificado antigo. 

## <a name="enforce-https"></a>Impor HTTPS

Por padrão, qualquer pessoa ainda pode aceder à sua aplicação usando HTTP. Pode redirecionar todos os pedidos HTTP para a porta HTTPS.

Na página da sua aplicação, na navegação à esquerda, selecione **as definições SSL**. Em seguida, em **HTTPS Apenas**, selecione **Ativado**.

![Impor HTTPS](./media/configure-ssl-bindings/enforce-https.png)

Quando a operação for concluída, navegue para um dos URLs HTTP que apontam para a sua aplicação. Por exemplo:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>Impor versões do TLS

A aplicação permite o [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2 por predefinição, o que é o nível do TLS recomendado pelas normas do setor, como [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Para impor versões do TLS diferentes, siga estes passos:

Na página da sua aplicação, na navegação à esquerda, selecione **as definições SSL**. Em seguida, na **versão do TLS**, selecione a versão mínima do TLS que pretende. Esta definição controla apenas as chamadas de entrada. 

![Impor TLS 1.1 ou 1.2](./media/configure-ssl-bindings/enforce-tls1-2.png)

Quando a operação for concluída, a sua aplicação rejeita todas as ligações com versões do TLS inferiores.

## <a name="handle-ssl-termination"></a>Manuseie a rescisão do SSL

No Serviço de Aplicações, a rescisão do [SSL](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre nos equilibradores de carga da rede, pelo que todos os pedidos HTTPS chegam à sua aplicação como pedidos HTTP não encriptados. Se a lógica da sua aplicação necessitar de verificar se os pedidos do utilizador estão encriptados ou não, inspecione o cabeçalho `X-Forwarded-Proto`.

Guias de configuração específicos do idioma, como o guia de [configuração Linux Node.js,](containers/configure-language-nodejs.md#detect-https-session) mostram-lhe como detetar uma sessão HTTPS no seu código de aplicação.

## <a name="automate-with-scripts"></a>Automatizar com scripts

### <a name="azure-cli"></a>CLI do Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Mais recursos

* [Utilize um certificado SSL no seu código de aplicação](configure-ssl-certificate-in-code.md)
* [FAQ : Certificados de serviço de aplicações](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
