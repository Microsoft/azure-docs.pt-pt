---
title: Implementar e configurar a pré-visualização Azure Firewall Premium
description: Saiba como implementar e configurar o Azure Firewall Premium.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: fa106fac683619706f4be330ad1c4bff7b56f2dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101721791"
---
# <a name="deploy-and-configure-azure-firewall-premium-preview"></a>Implementar e configurar a pré-visualização Azure Firewall Premium

> [!IMPORTANT]
> O Azure Firewall Premium está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 A Azure Firewall Premium Preview é uma firewall de próxima geração com capacidades que são necessárias para ambientes altamente sensíveis e regulados. Inclui as seguintes características:

- **Inspeção TLS** - desencripta o tráfego de saída, processa os dados, em seguida, encripta os dados e envia-os para o destino.
- **IDPS** - Um sistema de deteção e prevenção de intrusões em rede (IDPS) permite monitorizar atividades de rede para atividades maliciosas, registar informações sobre esta atividade, denunciá-la e tentar bloqueá-la opcionalmente.
- **Filtragem de URL** - estende a capacidade de filtragem FQDN da Azure Firewall para considerar um URL inteiro. Por exemplo, `www.contoso.com/a/c` em vez `www.contoso.com` de. .
- **Categorias web** - os administradores podem permitir ou negar o acesso do utilizador a categorias de websites, tais como sites de jogos, sites de redes sociais, entre outros.

Para mais informações, consulte [as funcionalidades Azure Firewall Premium](premium-features.md).

Você usará um modelo para implementar um ambiente de teste que tem um VNet central (10.0.0.0/16) com três sub-redes:
- uma sub-rede do trabalhador (10.0.10.0/24)
- uma sub-rede Azure Bastion (10.0.20.0/24)
- uma sub-rede de firewall (10.0.100.0/24)

Um único VNet central é utilizado neste ambiente de teste para a simplicidade. Para fins de produção, um [hub e topologia falada](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) com VNets espreitados é mais comum.

:::image type="content" source="media/premium-deploy/premium-topology.png" alt-text="Topologia central do VNet":::

A máquina virtual do trabalhador é um cliente que envia pedidos HTTP/S através da firewall.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="deploy-the-infrastructure"></a>Implementar a infraestrutura

O modelo implementa um ambiente de teste completo para Azure Firewall Premium habilitado com IDPS, Inspeção TLS, Filtragem de URL e Categorias Web:

- uma nova Política Azure Firewall Premium e Firewall com definições predefinidas para permitir uma validação fácil das suas capacidades centrais (IDPS, Inspeção TLS, Filtragem de URL e Categorias Web)
- implementa todas as dependências, incluindo o Cofre-Chave e uma Identidade Gerida. Num ambiente de produção, estes recursos podem já ser criados e não necessários no mesmo modelo.
- gera Auto-assinado Root CA e implanta-o no Cofre chave gerado
-  gera um CA intermédio derivado e implanta-o numa máquina virtual de teste do Windows (WorkerVM)
- um Bastion Host (BastionHost) também está implantado e pode ser usado para ligar à máquina de testes do Windows (WorkerVM)



[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-premium%2Fazuredeploy.json)

## <a name="test-the-firewall"></a>Testar a firewall

Agora pode testar categorias de IDPS, TLS, filtragem web e Web.

### <a name="add-firewall-diagnostics-settings"></a>Adicionar definições de diagnóstico de firewall

Para recolher registos de firewall, é necessário adicionar definições de diagnóstico para recolher registos de firewall.

1. Selecione a **DemoFirewall** e em **Monitorização,** selecione **Definições de Diagnóstico**.
2. Selecione **Adicionar definição de diagnóstico**.
3. Para **o nome de definição de diagnóstico,** escreva *fw-diag*.
4. No **registo**, selecione **AzureFirewallApplicationRule** e **AzureFirewallNetworkRule**.
5. Nos **detalhes do Destino**, selecione Enviar para Log Analytics espaço de **trabalho**.
6. Selecione **Guardar**.

### <a name="idps-tests"></a>Testes IDPS

Para testar o IDPS, terá de implantar o seu próprio servidor Web interno com um certificado de servidor apropriado. Para obter mais informações sobre os requisitos do certificado de pré-visualização Azure Firewall Premium, consulte [os certificados de pré-visualização Azure Firewall Premium](premium-certificates.md).

Pode utilizar `curl` para controlar vários cabeçalhos HTTP e simular tráfego malicioso.

#### <a name="to-test-idps-for-http-traffic"></a>Para testar iDPS para tráfego HTTP:

1. Na máquina virtual WorkerVM, abra uma janela de pedido de comando do administrador.
2. Digite o seguinte comando na solicitação de comando:

   `curl -A "BlackSun" <your web server address>`
3. Verá a resposta do seu servidor Web.
4. Aceda aos registos de regras da Rede firewall no portal Azure para encontrar um alerta semelhante à seguinte mensagem:

   :::image type="content" source="media/premium-deploy/alert-message.png" alt-text="Mensagem de alerta":::

   > [!NOTE]
   > Pode levar algum tempo para que os dados comecem a aparecer nos registos. Dê-lhe pelo menos 20 minutos para permitir que os registos comecem a mostrar os dados.
5. Adicione uma regra de assinatura para assinatura 2008983:

   1. Selecione a **DemoFirewallPolicy** e em **Definições** selecione **IDPS (pré-visualização)**.
   1. Selecione o separador **Regras Signature.**
   1. Em **Assinatura ID**, na caixa de texto aberta tipo *2008983*.
   1. Em **Modo**, selecione **Deny**.
   1. Selecione **Guardar**.
   1. Aguarde que o destacamento esteja concluído antes de prosseguir.



6. No WorkerVM, volte a `curl` comandar:

   `curl -A "BlackSun" <your web server address>`

   Uma vez que o pedido HTTP está agora bloqueado pela firewall, verá a seguinte saída após o fim do tempo limite de ligação:

   `read tcp 10.0.100.5:55734->10.0.20.10:80: read: connection reset by peer`

7. Vá aos registos do Monitor no portal Azure e encontre a mensagem para o pedido bloqueado.
<!---8. Now you can bypass the IDPS function using the **Bypass list**.

   1. On the **IDPS (preview)** page, select the **Bypass list** tab.
   2. Edit **MyRule** and set **Destination** to *10.0.20.10, which is the ServerVM private IP address.
   3. Select **Save**.
1. Run the test again: `curl -A "BlackSun" http://server.2020-private-preview.com` and now you should get the `Hello World` response and no log alert. --->

#### <a name="to-test-idps-for-https-traffic"></a>Para testar iDPS para tráfego HTTPS

Repita estes testes de caracóis utilizando HTTPS em vez de HTTP. Por exemplo:

`curl --ssl-no-revoke -A "BlackSun" <your web server address>`

Deve ver os mesmos resultados que teve com os testes HTTP.

### <a name="tls-inspection-with-url-filtering"></a>Inspeção TLS com filtragem de URL

Utilize os seguintes passos para testar a inspeção TLS com filtragem de URL.

1. Edite as regras de aplicação da política de firewall e adicione uma nova regra chamada `AllowURL` à `AllowWeb` coleção de regras. Configure o URL alvo `www.nytimes.com/section/world` , endereço IP de origem **\* *_,* tipo de destino _ URL (pré-visualização)**, selecione a **inspeção TLS (pré-visualização)** e protocolos **https https**.

3. Quando a implementação estiver concluída, abra um browser no WorkerVM e `https://www.nytimes.com/section/world` vá validar que a resposta HTML é exibida como esperado no navegador.
4. No portal Azure, pode ver todo o URL nos registos de monitorização da regra de aplicação:

      :::image type="content" source="media/premium-deploy/alert-message-url.png" alt-text="Mensagem de alerta mostrando o URL":::

Algumas páginas HTML podem parecer incompletas porque se referem a outros URLs que são negados. Para resolver esta questão, pode ser tomada a seguinte abordagem:

- Se a página HTML contiver ligações a outros domínios, pode adicionar estes domínios a uma nova regra de aplicação com acesso a estes FQDNs.
- Se a página HTML contiver links para sub URLs, então pode modificar a regra e adicionar um asterisco ao URL. Por exemplo: `targetURLs=www.nytimes.com/section/world*`

   Em alternativa, pode adicionar um novo URL à regra. Por exemplo: 

   `www.nytimes.com/section/world, www.nytimes.com/section/world/*`

### <a name="web-categories-testing"></a>Teste de categorias web

Vamos criar uma regra de aplicação para permitir o acesso a sites desportivos.
1. A partir do portal, abra o seu grupo de recursos e selecione **DemoFirewallPolicy**.
2. Selecione **Regras de Aplicação** e, em seguida, **Adicione uma coleção de regras**.
3. Para **nome**, tipo *GeneralWeb*, **Prioridade** *103*, **Grupo de recolha de regras** selecione **DefaultApplicationRuleCollectionGroup**.
4. De **acordo com as Regras** para o Tipo de **Nome** *Permite Desportos*, **Fonte** *\** , **Protocolo** *https*, **selecione inspeção TLS**, **Destino Tipo** selecione *categorias Web (pré-visualização)*, **Destination** select *Sports*.
5. Selecione **Adicionar**.

      :::image type="content" source="media/premium-deploy/web-categories.png" alt-text="Categoria de web de esportes":::
6. Quando a implementação estiver concluída, vá ao  **WorkerVM** e abra um navegador web e navegue para `https://www.nfl.com` .

   Você deve ver a página web da NFL, e o registo de regras de aplicação mostra que uma **categoria web:** regra desportiva foi correspondida e o pedido foi permitido.

## <a name="next-steps"></a>Passos seguintes

- [Pré-visualização premium Azure Firewall no portal Azure](premium-portal.md)