---
title: Utilize o Gateway de Aplicações Azure para proteger as suas aplicações web na Solução VMware Azure
description: Configure Azure Application Gateway para expor de forma segura as suas aplicações web em execução na Solução VMware Azure.
ms.topic: how-to
ms.date: 07/31/2020
ms.openlocfilehash: d4e193c58c5eccb29f603c3b4d56a09d26686975
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750594"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>Utilize o Gateway de Aplicações Azure para proteger as suas aplicações web na Solução VMware Azure

[O Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) é um balanceador de carga de tráfego web de camada 7 que lhe permite gerir o tráfego para as suas aplicações web. Oferece muitas capacidades: afinidade de sessão baseada em cookies, encaminhamento baseado em URL e Firewall de Aplicação Web (WAF) para citar alguns. (Para obter uma lista completa de funcionalidades, consulte [as funcionalidades do Gateway de aplicações Azure](../application-gateway/features.md).) É oferecido em duas versões, v1 e v2. Ambos foram testados com aplicações web em execução na Azure VMware Solution.

Neste artigo, vamos percorrer um cenário comum usando o Application Gateway em frente a uma fazenda de servidores web com um conjunto de configurações e recomendações para proteger uma aplicação web em execução na Azure VMware Solution. 

## <a name="topology"></a>Topologia
Como mostrado na seguinte figura, o Application Gateway pode ser usado para proteger máquinas virtuais Azure IaaS, conjuntos de escala de máquina virtual Azure ou servidores no local. As máquinas virtuais Azure VMware Solution serão tratadas como servidores no local pelo Application Gateway.

![O Application Gateway protege os VMs da Solução VMware Azure.](media/protect-avs-web-apps-with-app-gw/app-gateway-protects.png)

> [!IMPORTANT]
> A Azure Application Gateway é atualmente o único método suportado para expor aplicações web em execução em máquinas virtuais Azure VMware Solution.

O diagrama seguinte mostra o cenário de teste utilizado para validar o Gateway de Aplicações com aplicações web Azure VMware Solution.

![Integração de Gateway de aplicações com Azure VMware Solution executando aplicações web.](media/protect-avs-web-apps-with-app-gw/app-gateway-avs-scenario.png)

A instância 'Application Gateway' é implantada no hub numa sub-rede dedicada. Tem um endereço IP público Azure; recomenda-se ativar a proteção DDoS standard para a rede virtual. O servidor web está hospedado numa nuvem privada Azure VMware Solution atrás de routers NSX T0 e T1. A Azure VMware Solution utiliza [o ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) para permitir a comunicação com o hub e sistemas no local.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa.
- Uma nuvem privada Azure VMware Solution implantada e em funcionamento.

## <a name="deployment-and-configuration"></a>Implantação e configuração

1. No portal Azure, procure o **Gateway de Aplicações** e selecione **Criar porta de aplicação.**

2. Fornecer os dados básicos como no seguinte número; em seguida, selecione **Next:Frontends>**. 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/create-app-gateway.png" alt-text="Criação de Gateway de Aplicação":::

3. Escolha o tipo de endereço IP frontend. Para o público, escolha um endereço IP público existente ou crie um novo. Selecione **Seguinte:Backends>**.

    > [!NOTE]
    > Apenas skus standard e Web Application Firewall (WAF) são suportados para frontends privados.

4. Em seguida, adicione um pool de backend, que descreve um conjunto de instâncias que fazem parte da aplicação ou serviço (neste caso, máquinas virtuais em funcionamento na infraestrutura Azure VMware Solution). Forneça os detalhes dos servidores web em execução na nuvem privada Azure VMware Solution e selecione **Add**; em seguida, selecione **Seguinte:Configuração>**.

1. No **separador Configuração,** **selecione Adicione uma regra de encaminhamento**.

6. No **separador Listener,** forneça os detalhes para o ouvinte. Se o HTTPS for selecionado, deve ser fornecido um certificado, seja a partir de um ficheiro PFX ou de um certificado existente do Azure Key Vault. 

7. Selecione o separador **alvos de Backend** e selecione o pool de backend previamente criado. Para o campo **de definições HTTP,** selecione **Adicione novo**.

8. Configure os parâmetros para as definições HTTP. Selecione **Adicionar**.

9. Se pretender configurar regras baseadas em caminhos, **selecione Adicione vários alvos para criar uma regra baseada em caminhos**. 

10. Adicione uma regra baseada em caminhos e selecione **Adicionar**. Repita para adicionar regras adicionais baseadas em caminhos. 

11. Quando terminar de adicionar regras baseadas em caminhos, **selecione Adicionar** novamente; em seguida, selecione **Next:Tags>**. 

12. Adicione as etiquetas desejadas. Selecione **Seguinte:Rever + Criar>**.

13. Uma validação será executada no seu Gateway de Aplicações; se for bem sucedido, **selecione Criar** para implementar.

## <a name="configuration-examples"></a>Exemplos de configuração

Nesta secção, você aprenderá a configurar o Application Gateway com máquinas virtuais Azure VMware Solution como as piscinas de backend para os seguintes casos de utilização: 

- [Hospedar vários sites](#hosting-multiple-sites)
- [Encaminhamento por URL](#routing-by-url)

### <a name="hosting-multiple-sites"></a>Hospedar vários sites

Pode utilizar o portal Azure para configurar vários websites quando criar um gateway de aplicações. Neste tutorial, você define piscinas de endereços de backend usando máquinas virtuais que executam numa nuvem privada Azure VMware Solution em um gateway de aplicações existente. O gateway de aplicações faz parte de uma rede virtual Hub, tal como descrito na [Integra Azure VMware Solution num hub e arquitetura de fala.](concepts-avs-hub-and-spoke-integration.md) Este tutorial pressupõe que possui vários domínios, e usa exemplos de www.contoso.com e www.fabrikam.com.

1. Crie as máquinas virtuais. Na nuvem privada Azure VMware Solution, crie duas piscinas diferentes de máquinas virtuais; um vai representar Contoso e o segundo Fabrikam. 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs.png" alt-text="Crie as máquinas virtuais.":::

    Para ilustrar este tutorial, utilizamos o Windows Server 2016 com a função Internet Information Services (IIS) instalada. Uma vez instaladas as máquinas virtuais, executar os seguintes comandos PowerShell para configurar o IIS em cada um dos VMs. 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. Adicione as piscinas de backend. Numa instância de gateway de aplicação existente, selecione **piscinas backend** do menu esquerdo, selecione  **Add**, e insira os detalhes das novas piscinas. **Selecione Adicione** o painel direito.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-02.png" alt-text="Adicione as piscinas de backend." lightbox="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-02.png":::

3. Na secção **Ouvintes,** crie um novo ouvinte para cada website. Introduza os detalhes para cada ouvinte e selecione **Adicionar**.

4. Na navegação à esquerda, selecione **as definições HTTP** e selecione **Adicione** no painel esquerdo. Preencha os detalhes para criar uma nova definição HTTP e **selecione Guardar**.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-03.png" alt-text="Preencha os detalhes para criar uma nova definição HTTP e selecione Guardar." lightbox="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-03.png":::

5. Crie as regras na secção **Regra do** menu esquerdo. Associar cada regra ao ouvinte correspondente. Selecione **Adicionar**.

6. Configure as definições correspondentes do pool de backend e HTTP. Selecione **Adicionar**.

7. Teste a ligação. Abra o seu navegador preferido e navegue para os diferentes websites hospedados no seu ambiente Azure VMware Solution, por http://www.fabrikam.com exemplo.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-07.png" alt-text="Teste a ligação.":::

### <a name="routing-by-url"></a>Encaminhamento por URL

Pode utilizar o Gateway de aplicação Azure para configurar as regras de encaminhamento baseadas em caminhos de URL. Neste tutorial, você define piscinas de endereços de backend usando máquinas virtuais que executam numa nuvem privada Azure VMware Solution em um gateway de aplicações existente. O gateway de aplicações faz parte de uma rede virtual Hub, conforme descrito na documentação de [integração nativa Azure VMware Solution Azure.](concepts-avs-hub-and-spoke-integration.md) Em seguida, cria regras de encaminhamento que garantem que o tráfego web chega aos servidores apropriados nas piscinas.

1. Crie as máquinas virtuais. Na nuvem privada Azure VMware Solution, crie um conjunto de máquinas virtuais para representar a quinta web. 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs.png" alt-text="Crie um conjunto de máquinas virtuais na Azure VMware Solution.":::

    O Windows Server 2016 com a função IIS instalada foi utilizado para ilustrar este tutorial. Assim que as máquinas virtuais estiverem instaladas, executar os seguintes comandos PowerShell para configurar o IIS para o tutorial em cada VM. 

    A primeira máquina virtual, contoso-web-01, irá acolher o site principal.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

    A segunda máquina virtual, contoso-web-02, irá acolher o site de imagens.
 
    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "images" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\images\test.htm -Value $($env:computername)
    ```

    A terceira máquina virtual, contoso-web-03, irá acolher o site de vídeo.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "video" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\video\test.htm -Value $($env:computername)
    ```

2. Adicione as piscinas de backend. Você precisará adicionar três novos pools de backend em uma instância de gateway de aplicação existente. Selecione **piscinas backend** do menu esquerdo. **Selecione Adicionar** e insira os detalhes da primeira piscina, **contoso-web**. Adicione um VM como alvo. Selecione **Adicionar**. Repita este processo para **contoso-imagens** e **contoso-vídeo,** adicionando um VM único a cada um como alvo. 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-02.png" alt-text="Adicione três novas piscinas de backend." lightbox="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-02.png":::

3. Na secção **Ouvinte,** crie um novo ouvinte do tipo Basic utilizando a porta 8080.

4. Na navegação à esquerda, selecione **as definições HTTP** e selecione **Adicione** no painel esquerdo. Preencha os detalhes para criar uma nova definição HTTP e **selecione Guardar**.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-04.png" alt-text="Configuração de definições HTP.":::

5. Crie as regras na secção **Regra do** menu esquerdo. Associe cada regra ao ouvinte previamente criado. Em seguida, configufique as definições principais de backend pool e HTTP. Selecione **Adicionar**.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-07.png" alt-text="Crie as regras na secção Regra do menu esquerdo.":::

6. Teste a configuração. Aceda ao portal de aplicações no portal Azure e na secção **Visão Geral,** copie o endereço IP público. Em seguida, abra uma nova janela do navegador e introduza o URL `http://<app-gw-ip-address>:8080` . 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-08.png" alt-text="Teste a configuração a partir do portal Azure.":::

    Altere o URL para `http://<app-gw-ip-address>:8080/images/test.htm`.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-09.png" alt-text="Mude a URL.":::

    Altere novamente o URL para `http://<app-gw-ip-address>:8080/video/test.htm` .

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-10.png" alt-text="Mude o URL novamente.":::

## <a name="next-steps"></a>Passos Seguintes

Reveja a documentação do [Gateway de Aplicação Azure](https://docs.microsoft.com/azure/application-gateway/) para obter mais exemplos de configuração.
