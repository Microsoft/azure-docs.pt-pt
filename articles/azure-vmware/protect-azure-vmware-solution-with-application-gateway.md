---
title: Utilize o Gateway de Aplicações Azure para proteger as suas aplicações web na Solução VMware Azure
description: Configure Azure Application Gateway para expor de forma segura as suas aplicações web em execução na Solução VMware Azure.
ms.topic: how-to
ms.date: 10/13/2020
ms.openlocfilehash: 7956ea51421f5cfa893942401c1d9a5871039689
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578489"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>Utilize o Gateway de Aplicações Azure para proteger as suas aplicações web na Solução VMware Azure

[Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) é um balanceador de carga de tráfego web de camada 7 que permite gerir o tráfego para as suas aplicações web. É oferecido tanto na Azure VMware Solution v1.0 como v2.0. Ambas as versões testadas com aplicações web em execução na Solução VMware Azure.

As capacidades incluem: 
- Afinidade da sessão baseada em cookies
- Encaminhamento com base no URL
- Firewall de Aplicações Web (WAF)

Para obter uma lista completa de funcionalidades, consulte [as funcionalidades do Gateway de Aplicações Azure](../application-gateway/features.md). 

Este artigo mostra-lhe como usar o Applicate Gateway em frente a uma fazenda de servidores web para proteger uma aplicação web em execução na Solução VMware Azure. 

## <a name="topology"></a>Topologia
O diagrama mostra como o Application Gateway é usado para proteger máquinas virtuais Azure IaaS (VMs), conjuntos de balanças de máquinas virtuais Azure ou servidores no local. Application Gateway trata VMs de Solução VMware Azure como servidores no local. 

![O Application Gateway protege os VMs da Solução VMware Azure.](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-protects.png)

> [!IMPORTANT]
> A Azure Application Gateway é atualmente o único método suportado para expor aplicações web em execução em VMs Azure VMware Solution.

O diagrama mostra o cenário de teste utilizado para validar o Gateway de Aplicações com aplicações web Azure VMware Solution.

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="Integração de Gateway de Aplicação com Azure VMware Solution que executam aplicações web" border="false":::

A instância 'Application Gateway' é implantada no hub numa sub-rede dedicada. Tem um endereço IP público Azure. Recomenda-se ativar a proteção DDoS standard para a rede virtual. O servidor web está hospedado numa nuvem privada Azure VMware Solution atrás de routers NSX T0 e T1. A Azure VMware Solution utiliza [o ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) para permitir a comunicação com o hub e sistemas no local.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa.
- Uma nuvem privada Azure VMware Solution implantada e em funcionamento.

## <a name="deployment-and-configuration"></a>Implantação e configuração

1. No portal Azure, procure o **Gateway de Aplicações** e selecione **Criar porta de aplicação.**

2. Fornecer os dados básicos como no seguinte número; em seguida, selecione **Seguinte: Frontends>**. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/create-app-gateway.png" alt-text="Criação de Gateway de Aplicação":::

3. Escolha o tipo de endereço IP frontend. Para o público, escolha um endereço IP público existente ou crie um novo. Selecione **Seguinte: Backends>**.

    > [!NOTE]
    > Apenas skus standard e Web Application Firewall (WAF) são suportados para frontends privados.

4. Adicione um pool de backend dos VMs que funcionam na infraestrutura Azure VMware Solution. Forneça os detalhes dos servidores web que funcionam na nuvem privada Azure VMware Solution e selecione **Add**.  Em seguida, selecione **Seguinte: Configuração>**.

1. No **separador Configuração,** **selecione Adicione uma regra de encaminhamento**.

6. No **separador Listener,** forneça os detalhes para o ouvinte. Se o HTTPS for selecionado, deve fornecer um certificado, seja a partir de um ficheiro PFX ou de um certificado Azure Key Vault existente. 

7. Selecione o separador **alvos de Backend** e selecione o pool de backend previamente criado. Para o campo **de definições HTTP,** selecione **Adicione novo**.

8. Configure os parâmetros para as definições HTTP. Selecione **Adicionar**.

9. Se pretender configurar regras baseadas em caminhos, **selecione Adicione vários alvos para criar uma regra baseada em caminhos**. 

10. Adicione uma regra baseada em caminhos e selecione **Adicionar**. Repita para adicionar regras adicionais baseadas em caminhos. 

11. Quando terminar de adicionar regras baseadas em caminhos, **selecione Adicionar** novamente; em seguida, selecione **Seguinte: Tags>**. 

12. Adicione tags e, em seguida, selecione **Seguinte: Rever + Criar>**.

13. Uma validação será executada no seu Gateway de Aplicações; se for bem sucedido, **selecione Criar** para implementar.

## <a name="configuration-examples"></a>Exemplos de configuração

Nesta secção, você vai aprender a configurar o Application Gateway com VMs Azure VMware Solution como os pools backend para estes casos de utilização: 

- [Hospedar vários sites](#hosting-multiple-sites)
- [Encaminhamento por URL](#routing-by-url)

### <a name="hosting-multiple-sites"></a>Hospedar vários sites

Este procedimento mostra-lhe como definir piscinas de endereços de backend usando VMs em execução numa nuvem privada Azure VMware Solution em um gateway de aplicações existente. 

>[!NOTE]
>Este procedimento pressupõe que você tem vários domínios, então usaremos exemplos de www.contoso.com e www.fabrikam.com.


1. Na sua nuvem privada, crie duas piscinas diferentes de VMs. Um representa Contoso e o segundo Fabrikam. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs.png" alt-text="Criar os VMs.":::

    Usamos o Windows Server 2016 com a função internet Information Services (IIS) instalada para ilustrar este tutorial. Uma vez instalados os VMs, executar os seguintes comandos PowerShell para configurar o IIS em cada um dos VMs. 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. Numa instância de gateway de aplicação existente, selecione **piscinas backend** do menu esquerdo, selecione  **Add** , e insira os detalhes das novas piscinas. **Selecione Adicione** o painel direito.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-02.png" alt-text="Adicione as piscinas de backend." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-02.png":::

3. Na secção **Ouvintes,** crie um novo ouvinte para cada website. Introduza os detalhes para cada ouvinte e selecione **Adicionar**.

4. À esquerda, selecione **as definições HTTP** e selecione **Adicione** no painel esquerdo. Preencha os detalhes para criar uma nova definição HTTP e **selecione Guardar**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-03.png" alt-text="Preencha os detalhes para criar uma nova definição HTTP e selecione Guardar." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-03.png":::

5. Crie as regras na secção **Regra do** menu esquerdo. Associar cada regra ao ouvinte correspondente. Selecione **Adicionar**.

6. Configure as definições correspondentes do pool de backend e HTTP. Selecione **Adicionar**.

7. Teste a ligação. Abra o seu navegador preferido e navegue para os diferentes websites hospedados no seu ambiente Azure VMware Solution, por exemplo, http://www.fabrikam.com .

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-07.png" alt-text="Teste a ligação.":::

### <a name="routing-by-url"></a>Encaminhamento por URL

Este procedimento mostra-lhe como definir piscinas de endereços de backend usando VMs em execução numa nuvem privada Azure VMware Solution em um gateway de aplicações existente. Em seguida, cria regras de encaminhamento que garantem que o tráfego web chega aos servidores apropriados nas piscinas.

1. Na sua nuvem privada, crie uma piscina de máquinas virtuais para representar a web farm. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs.png" alt-text="Crie um conjunto de VMs na Azure VMware Solution.":::

    O Windows Server 2016 com a função IIS instalada foi utilizado para ilustrar este tutorial. Uma vez instalados os VMs, executar os seguintes comandos PowerShell para configurar o IIS para cada tutorial VM. 

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

2. Adicione três novos pools de backend numa instância de gateway de aplicação existente. 

   1. Selecione **piscinas backend** do menu esquerdo. 
   1. **Selecione Adicionar** e insira os detalhes da primeira piscina, **contoso-web**. 
   1. Adicione um VM como alvo. 
   1. Selecione **Adicionar**. 
   1. Repita este processo para **contoso-imagens** e **contoso-vídeo,** adicionando um VM único como alvo. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-02.png" alt-text="Adicione três novas piscinas de backend." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-02.png":::

3. Na secção **Ouvinte,** crie um novo ouvinte do tipo Basic utilizando a porta 8080.

4. Na navegação à esquerda, selecione **as definições HTTP** e selecione **Adicione** no painel esquerdo. Preencha os detalhes para criar uma nova definição HTTP e **selecione Guardar**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-04.png" alt-text="Configuração de definições HTP.":::

5. Crie as regras na secção **Regra do** menu esquerdo. Associe cada regra ao ouvinte previamente criado. Em seguida, configufique as definições principais de backend pool e HTTP. Selecione **Adicionar**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-07.png" alt-text="Crie as regras na secção Regra do menu esquerdo.":::

6. Teste a configuração. Aceda ao gateway de aplicações no portal Azure e copie o endereço IP público na secção **Overview.** 

   1. Abra uma nova janela do navegador e introduza o URL `http://<app-gw-ip-address>:8080` . 

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-08.png" alt-text="Teste a configuração a partir do portal Azure.":::

   1. Altere o URL para `http://<app-gw-ip-address>:8080/images/test.htm`.

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-09.png" alt-text="Mude a URL.":::

   1. Altere novamente o URL para `http://<app-gw-ip-address>:8080/video/test.htm` .

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-10.png" alt-text="Mude o URL novamente.":::

## <a name="next-steps"></a>Passos Seguintes

Reveja a documentação do [Gateway de Aplicação Azure](../application-gateway/index.yml) para obter mais exemplos de configuração.