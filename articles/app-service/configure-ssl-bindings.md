---
title: Proteja um DNS personalizado com uma ligação TLS/SSL
description: Acesso HTTPS seguro ao seu domínio personalizado criando uma ligação TLS/SSL com um certificado. Melhorar a segurança do seu website aplicando HTTPS ou TLS 1.2.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 04/30/2020
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: a713d869a754f17f4030c531999c822a2cbb76ee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102039806"
---
# <a name="secure-a-custom-dns-name-with-a-tlsssl-binding-in-azure-app-service"></a>Proteja um nome DNS personalizado com uma ligação TLS/SSL no Azure App Service

Este artigo mostra-lhe como proteger o [domínio personalizado](app-service-web-tutorial-custom-domain.md) na sua app ou [aplicação de função](../azure-functions/index.yml) [App Service,](./index.yml) criando uma ligação de certificado. Quando terminar, pode aceder à sua aplicação De Serviço de Aplicações no ponto final para o `https://` seu nome DNS personalizado (por exemplo, `https://www.contoso.com` ). 

![Aplicativo Web com certificado TLS/SSL personalizado](./media/configure-ssl-bindings/app-with-custom-ssl.png)

Garantir um [domínio personalizado](app-service-web-tutorial-custom-domain.md) com um certificado envolve dois passos:

- [Adicione um certificado privado ao Serviço de Aplicações](configure-ssl-certificate.md) que satisfaça todos os [requisitos de certificado privado.](configure-ssl-certificate.md#private-certificate-requirements)
-  Crie uma ligação TLS ao domínio personalizado correspondente. Este segundo passo é abordado por este artigo.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Atualizar o escalão de preço da sua aplicação
> * Proteja um domínio personalizado com um certificado
> * Impor HTTPS
> * Impor TLS 1.1/1.2
> * Automatizar a gestão de TLS com scripts

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar este guia:

- [Criar uma aplicação do Serviço de Aplicações](./index.yml)
- [Mapeie um nome de domínio para a sua app](app-service-web-tutorial-custom-domain.md) ou compre e [configuure-o em Azure](manage-custom-dns-buy-domain.md)
- [Adicione um certificado privado à sua aplicação](configure-ssl-certificate.md)

> [!NOTE]
> A forma mais fácil de adicionar um certificado privado é [criar um Certificado Gerido de Serviço de Aplicações gratuita](configure-ssl-certificate.md#create-a-free-managed-certificate-preview) (Pré-visualização).

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Proteja um domínio personalizado

Faça os seguintes passos:

No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>a partir do menu esquerdo, selecione **Serviços de Aplicações**  >  **\<app-name>** .

A partir da navegação à esquerda da sua aplicação, inicie o diálogo **de ligação TLS/SSL** por:

- Seleção de **domínios personalizados**  >  **Adicionar encadernação**
- Selecionando **definições TLS/SSL**  >  **Adicionar ligação TLS/SSL**

![Adicionar ligação ao domínio](./media/configure-ssl-bindings/secure-domain-launch.png)

Em **Domínio Personalizado,** selecione o domínio personalizado para o quais pretende adicionar uma ligação.

Se a sua aplicação já tiver um certificado para o domínio personalizado selecionado, vá diretamente para [criar a ligação.](#create-binding) Caso contrário, continue.

### <a name="add-a-certificate-for-custom-domain"></a>Adicione um certificado para domínio personalizado

Se a sua aplicação não tiver certificado para o domínio personalizado selecionado, então tem duas opções:

- **Upload PFX Certificate** - Siga o fluxo de trabalho no [Upload de um certificado privado](configure-ssl-certificate.md#upload-a-private-certificate)e, em seguida, selecione esta opção aqui.
- **Certificado de Serviço de Aplicações de Importação** - Siga o fluxo de trabalho no [Certificado de Serviço de Aplicações de Importação](configure-ssl-certificate.md#import-an-app-service-certificate)e, em seguida, selecione esta opção aqui.

> [!NOTE]
> Também pode [Criar um certificado gratuito](configure-ssl-certificate.md#create-a-free-managed-certificate-preview) (Pré-visualização) ou importar um certificado de Cofre de [Chaves,](configure-ssl-certificate.md#import-a-certificate-from-key-vault)mas deve fazê-lo separadamente e, em seguida, voltar ao diálogo **de ligação TLS/SSL.**

### <a name="create-binding"></a>Criar ligação

Utilize a tabela seguinte para o ajudar a configurar a ligação TLS no diálogo **de ligação TLS/SSL** e, em seguida, clique em **Adicionar Ligação**.

| Definições | Descrição |
|-|-|
| Domínio personalizado | O nome de domínio para adicionar a ligação TLS/SSL para. |
| Impressão digital do certificado privado | O certificado para ligar. |
| Tipo TLS/SSL | <ul><li>**[SNI SSL](https://en.wikipedia.org/wiki/Server_Name_Indication)** - Podem ser adicionadas ligações SNI SSL múltiplas. Esta opção permite que vários certificados TLS/SSL garantam vários domínios no mesmo endereço IP. A maioria dos navegadores modernos (incluindo Internet Explorer, Chrome, Firefox e Opera) suportam SNI (para mais informações, consulte [a indicação do nome do servidor).](https://wikipedia.org/wiki/Server_Name_Indication)</li><li>**IP SSL** - Apenas uma ligação IP SSL pode ser adicionada. Esta opção permite apenas um certificado TLS/SSL para garantir um endereço IP público dedicado. Depois de configurar a ligação, siga os passos nos [registos remap para IP SSL](#remap-records-for-ip-ssl).<br/>O IP SSL é suportado apenas no nível **Standard** ou acima. </li></ul> |

Uma vez concluída a operação, o estado TLS/SSL do domínio personalizado é alterado para **Secure**.

![Ligação TLS/SSL bem sucedida](./media/configure-ssl-bindings/secure-domain-finished.png)

> [!NOTE]
> Um estado **Secure** nos **domínios Personalizados** significa que está protegido com um certificado, mas o Serviço de Aplicações não verifica se o certificado é auto-assinado ou expirado, por exemplo, o que também pode fazer com que os navegadores mostrem um erro ou aviso.

## <a name="remap-records-for-ip-ssl"></a>Remap registos para IP SSL

Se não utilizar o IP SSL na sua aplicação, ignore o [Teste HTTPS para o seu domínio personalizado.](#test-https)

Há duas mudanças que precisa de fazer, potencialmente:

- Por predefinição, a sua aplicação utiliza um endereço IP público partilhado. Quando liga um certificado ao IP SSL, o Serviço de Aplicações cria um novo endereço IP dedicado para a sua aplicação. Se mapeou um registo A para a sua aplicação, atualize o seu registo de domínio com este novo endereço IP dedicado.

    A página de **domínio personalizado** da sua aplicação é atualizada com o novo endereço IP dedicado. [Copie este endereço IP](app-service-web-tutorial-custom-domain.md#info) e [remapeie o registo A ](app-service-web-tutorial-custom-domain.md#map-an-a-record) para este endereço IP novo.

- Se tiver uma ligação SNI SSL `<app-name>.azurewebsites.net` para, [remape qualquer mapeamento CNAME](app-service-web-tutorial-custom-domain.md#map-a-cname-record) para apontar `sni.<app-name>.azurewebsites.net` em vez disso (adicione o `sni` prefixo).

## <a name="test-https"></a>Tester HTTPS

Em vários navegadores, navegue para `https://<your.custom.domain>` verificar se serve a sua aplicação.

:::image type="content" source="./media/configure-ssl-bindings/app-with-custom-ssl.png" alt-text="Screenshot mostrando um exemplo de navegação para o seu domínio personalizado com o URL de contoso.com realçado.":::

O seu código de aplicação pode inspecionar o protocolo através do cabeçalho "x-appservice-proto". O cabeçalho terá um valor de `http` ou `https` . 

> [!NOTE]
> Se a sua aplicação lhe der erros de validação de certificados, provavelmente está a usar um certificado auto-assinado.
>
> Se não for esse o caso, poderá ter deixou de fora certificados intermédios quando exportou o certificado para o ficheiro PFX.

## <a name="prevent-ip-changes"></a>Prevenir alterações ip

O seu endereço IP de entrada pode ser alterado quando eliminar uma ligação, mesmo que essa ligação seja IP SSL. Isto é especialmente importante quando se renova um certificado que já está numa ligação IP SSL. Para evitar uma alteração de endereço IP da sua aplicação, siga estes passos por ordem:

1. Carregar o novo certificado.
2. Vincular o novo certificado com o domínio personalizado que pretende sem eliminar os antigo. Esta ação substitui o enlace em vez de remover o antigo.
3. Eliminar o certificado antigo. 

## <a name="enforce-https"></a>Impor HTTPS

Por predefinição, qualquer pessoa ainda pode aceder à sua aplicação utilizando HTTP. Pode redirecionar todos os pedidos HTTP para a porta HTTPS.

Na sua página de aplicações, na navegação à esquerda, selecione **as definições SSL**. Em seguida, em **HTTPS Apenas**, selecione **Ativado**.

![Impor HTTPS](./media/configure-ssl-bindings/enforce-https.png)

Quando a operação for concluída, navegue para um dos URLs HTTP que apontam para a sua aplicação. Por exemplo:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>Impor versões do TLS

A aplicação permite o [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2 por predefinição, o que é o nível do TLS recomendado pelas normas do setor, como [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Para impor versões do TLS diferentes, siga estes passos:

Na sua página de aplicações, na navegação à esquerda, selecione **as definições SSL**. Em seguida, na **versão do TLS**, selecione a versão mínima do TLS que pretende. Esta definição controla apenas as chamadas de entrada. 

![Impor TLS 1.1 ou 1.2](./media/configure-ssl-bindings/enforce-tls1-2.png)

Quando a operação for concluída, a sua aplicação rejeita todas as ligações com versões do TLS inferiores.

## <a name="handle-tls-termination"></a>Manusear rescisão de TLS

No Serviço de Aplicações, a [rescisão de TLS](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre nos equilibristas de carga de rede, pelo que todos os pedidos HTTPS chegam à sua aplicação como pedidos HTTP não encriptados. Se a lógica da sua aplicação precisar de verificar se os pedidos do utilizador estão encriptados ou não, inspecione o `X-Forwarded-Proto` cabeçalho.

Guias de configuração específicos do idioma, como o guia [de configuração Node.js Linux,](configure-language-nodejs.md#detect-https-session) mostram-lhe como detetar uma sessão HTTPS no seu código de aplicação.

## <a name="automate-with-scripts"></a>Automatizar com scripts

### <a name="azure-cli"></a>CLI do Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>Mais recursos

* [Utilizar um certificado TLS/SSL no código no Serviço de Aplicações do Azure](configure-ssl-certificate-in-code.md)
* [FAQ : Certificados de Serviço de Aplicações](./faq-configuration-and-management.md)