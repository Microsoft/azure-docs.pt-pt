---
title: Tutorial - Implementar e configurar VMware HCX
description: Aprenda a implementar e configurar a solução VMware HCX para a sua nuvem privada Azure VMware Solution.
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 69832d1537f0f1be95d3283f543ef6e54187b58d
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91583442"
---
# <a name="deploy-and-configure-vmware-hcx"></a>Implementar e configurar VMware HCX

Neste artigo, percorremos os procedimentos para implementar e configurar o VMware HCX para a sua nuvem privada Azure VMWare Solution. O VMware HCX permite a migração das suas cargas de trabalho VMware para a Azure VMware Solution e outros sites conectados através de vários tipos de migração.

VMware HCX Advanced (pré-implantado em Azure VMware Solution) suporta até três ligações do site (no local para nuvem, ou nuvem para nuvem). Se forem necessárias mais de três ligações de site para vMware HCX Enterprise, tem a opção de ativar o addon [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) (que está atualmente em *Pré-visualização)* apresentando um pedido de [suporte](https://rc.portal.azure.com/#create/Microsoft.Support). VMware HCX Enterprise Edition (EE) está disponível com a Azure VMware Solution como uma função/serviço *de pré-visualização.* Enquanto vMware HCX EE para Azure VMware Solution está em *Pré-visualização,* é uma função/serviço gratuito e sujeito a termos e condições de serviço de pré-visualização. Assim que o serviço VMware HCX EE for classificado, receberá um aviso de 30 dias de que a faturação irá mudar. Também terá a opção de desligar/excluir o serviço.

Antes de começar, reveja minuciosamente [Antes de começar,](#before-you-begin) [os requisitos de versão de software](#software-version-requirements)e [pré-requisitos](#prerequisites). 

Então, vamos percorrer todos os procedimentos necessários para:

> [!div class="checklist"]
> * Implementar as VMware HCX OVA (Connector)
> * Ativar VMware HCX
> * Emparelhe o seu ambiente no local com o seu ambiente Azure VMware Solution.
> * Configure a interligação (perfil de cálculo, perfil de rede e malha de serviço)
> * Configuração completa verificando o estado do aparelho.

Uma vez terminado, pode seguir os próximos passos recomendados no final deste artigo.  

## <a name="before-you-begin"></a>Before you begin
   
* Reveja a [série tutorial](tutorial-network-checklist.md)básica do Software Defined Datacenter (SDDC) do Azure VMware Solution .
* Reveja e refira a [documentação VMware HCX,](https://docs.vmware.com/en/VMware-HCX/index.html)incluindo o guia do utilizador HCX.
* Rever VMware docs [Migrando Máquinas Virtuais com VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* Rever opcionalmente [as considerações de implantação de VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Analise opcionalmente materiais VMware relacionados em HCX, como a [série de blogs](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) VMware vSphere em HCX. 
* Opcionalmente solicite uma ativação Azure VMware Solution HCX Enterprise através de canais de suporte Azure VMware Solution.
* Para implantar o aparelho WAN Interconnect, já estão atribuídos gamas CIDR específicas do cliente fornecido `\22` para a criação de nuvem privada.

Dimensionar cargas de trabalho contra os recursos de computação e armazenamento é um passo essencial de planeamento. Aborde o passo de dimensionamento como parte do planeamento inicial do ambiente em nuvem privada. 

Também pode dimensionar cargas de trabalho completando uma Avaliação de [Solução VMware Azure](../migrate/how-to-create-azure-vmware-solution-assessment.md) no portal Azure Migrate.

## <a name="software-version-requirements"></a>Requisitos de versão de software

Os componentes da infraestrutura devem estar a executar a versão mínima necessária. 
                                                         
| Tipo de componente    | Requisitos ambientais de origem    | Requisitos de ambiente de destino   |
| --- | --- | --- |
| vCenter Server   | 5.1<br/><br/>Se utilizar 5.5 U1 ou mais cedo, utilize a interface de utilizador HCX autónoma para operações de HCX.  | 6.0 U2 e superior   |
| ESXi   | 5.0    | ESXi 6.0 e superior   |
| NSX    | Para a extensão da rede HCX de comutadores lógicos na fonte: NSXv 6.2+ ou NSX-T 2.4+   | NSXv 6.2+ ou NSX-T 2.4+<br/><br/>Para o encaminhamento de proximidade HCX: NSXv 6.4+ (O Encaminhamento de proximidade não é suportado com NSX-T) |
| vCloud Diretor   | Não é necessário - sem interoperabilidade com vCloud Diretor no site de origem | Ao integrar o ambiente de destino com o vCloud Diretor, o mínimo é 9.1.0.2.  |

## <a name="prerequisites"></a>Pré-requisitos

### <a name="network-and-ports"></a>Rede e portas

* [ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md) configurado entre os circuitos Azure VMware Solution SDDC ExpressRoute.

* Todas as portas necessárias devem estar abertas para comunicação entre componentes no local e no local para Azure VMware Solution SDDC (ver [documentação VMware HCX).](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)

* Os aparelhos HCX IX e NE no local devem poder chegar à infraestrutura vCenter e ESXi.

### <a name="ip-addresses"></a>Endereços IP

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>Implementar o VMware HCX OVA no local

>[!NOTE]
>Antes de colocar o aparelho virtual no seu vCenter no local, terá de descarregar o VMware HCX OVA. 

1. Inscreva-se no Azure VMware Solution HCX Manager na `https://x.x.x.9` porta 443 com as credenciais de utilizador **cloudadmin** e, em seguida, vá para **Suporte**.

   >[!TIP]
   >Para identificar o endereço IP do HCX Manager, na lâmina Azure VMware Solution, vá à Conectividade **Gestão**  >  **Connectivity** e, em seguida, selecione o separador **HCX.** 
   >
   >A palavra-passe vCenter foi definida quando configura a nuvem privada.

1. Selecione o link **de descarregamento** para descarregar o ficheiro VMware HCX OVA.

1. Vá ao vCenter no local e selecione um modelo OVF, que é o ficheiro OVA que descarregou, para implementar no seu vCenter no local.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Selecione um nome e localização, um recurso/cluster onde está a implementar o HCX e, em seguida, reveja os detalhes e os recursos necessários.  

   :::image type="content" source="media/tutorial-vmware-hcx/configure-template.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local." lightbox="media/tutorial-vmware-hcx/configure-template.png":::

1. Reveja os termos da licença e, se concordar, selecione o armazenamento e a rede necessários e, em seguida, selecione **Next**.

1. No **modelo Personalizar,** insira todas as informações necessárias. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Selecione **Seguinte**, verifique a configuração e, em seguida, selecione **Finish** para implementar HCX OVA.
     
   >[!NOTE]
   >Geralmente, o VMware HCX Manager que está a implementar agora é implantado na rede de gestão do cluster.  
   
   > [!IMPORTANT]
   > Depois de concluída a colocação, poderá ter de ligar manualmente o aparelho virtual.
   > Aguarde 10-15 minutos depois de ligar o aparelho HCX para passar ao passo seguinte.

Para obter uma visão geral deste passo, consulte o vídeo de implementação do [Azure VMware Solution - VMware HCX Appliance.](https://www.youtube.com/embed/BwSnQeefnso) 


## <a name="activate-vmware-hcx"></a>Ativar VMware HCX

Depois de implementar o VMware HCX OVA no local, está pronto para ativar o VMware HCX. Antes de poder ativar o VMware HCX, terá de recuperar uma licença.

1. Na Solução VMware Azure, vá à Conectividade **Gestão,**  >  **Connectivity**selecione o separador **HCX** e, em seguida, selecione **Add**.

1. Inscreva-se no VMware HCX Manager no local `https://HCXManagerIP:9443` e assine as credenciais do utilizador **administrativo.** 

   > [!IMPORTANT]
   > Certifique-se de incluir o número de `9443` porta com o endereço IP VMware HCX Manager.

1. Em **Licenciamento,** insira a sua **Tecla Avançada HCX**.  
   
    > [!NOTE]
    > VMware HCX Manager deve ter acesso à internet aberto ou um proxy configurado.

1. Na **Localização do Datacenter,** forneça o local mais próximo onde instala o VMware HCX Manager no local.

1. Modifique o **Nome do Sistema** ou aceite o padrão.
   
1. Pode terminar mais tarde ou continuar, selecione a opção **Sim, Continue** a continuar.
    
1. Em **Connect your vCenter**, forneça o endereço FQDN ou IP do seu servidor vCenter e as credenciais apropriadas e, em seguida, selecione **Continue**.
   
1. Em **Configurar SSO/PSC,** forneça o endereço FQDN ou IP do seu CPE e, em seguida, selecione **Continue**.
   
   >[!NOTE]
   >Tipicamente o mesmo que o seu vCenter FQDN/IP.

1. Verifique se todas as entradas estão corretas e **selecione Restart**.
    
   > [!NOTE]
   > Vai sentir um atraso depois de reiniciar antes de ser solicitado para o próximo passo.
   >
   >Após o reinício dos serviços, é fundamental que veja o vCenter a mostrar-se como verde no ecrã que aparece. Tanto o vCenter como o SSO têm os parâmetros de configuração adequados, que devem ser os mesmos que o ecrã anterior.

   :::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Para uma visão geral deste passo, consulte o vídeo de [ativação Azure VMware Solution - VMware HCX.](https://www.youtube.com/embed/BkAV_TNYxdE)


## <a name="configure-vmware-hcx"></a>Configurar VMware HCX

Agora está pronto para adicionar um emparelhamento de site, criar um perfil de rede e computação, e ativar serviços, tais como migração, extensão de rede ou recuperação de desastres. 

### <a name="add-a-site-pairing"></a>Adicione um emparelhamento de site

Pode ligar (emparelhamento) o VMware HCX Manager em Azure VMware Solution com o VMware HCX Manager no seu datacenter. 

1. Inscreva-se no seu vCenter no local, e em **Casa,** selecione **HCX**.

   :::image type="content" source="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local." lightbox="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png":::

1. Em **Infraestruturas**, selecione **O Emparelhamento do Site**e, em seguida, selecione a opção **'Ligar ao Local Remoto'** (no meio do ecrã). 

   :::image type="content" source="media/tutorial-vmware-hcx/connect-remote-site.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local." lightbox="media/tutorial-vmware-hcx/connect-remote-site.png":::

1. Introduza o **endereço de URL ou IP remoto HCX**, o nome de utilizador e palavra-passe da Solução VMware Azure , cloudadmin@vsphere.local e, em seguida, selecione **Connect**. **password**

   > [!NOTE]
   > O **URL HCX Remoto** é o seu Azure VMware Solution private cloud HCX Manager, tipicamente o endereço ".9" da rede de gestão.  Por exemplo, se o seu vCenter for 192.168.4.2, então o URL HCX será de 192.168.4.9.
   >
   > A **palavra-passe** será a mesma palavra-passe que usou para iniciar ser no vCenter. Definiu esta palavra-passe no ecrã inicial de implantação.

   Vê um ecrã que mostra o seu HCX Manager na Solução VMware Azure e o seu HCX Manager no local ligado (emparelhado).

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local.":::

Para uma visão geral deste passo, consulte o vídeo de emparelhamento do [site Azure VMware - VMware HCX.](https://www.youtube.com/embed/sKizDCRHOko)



### <a name="create-network-profiles"></a>Criar perfis de rede

O VMware HCX implementa um par de aparelhos virtuais (automatizados), mas precisa de vários segmentos IP.  Ao criar os seus perfis de rede, define os segmentos IP, que identificou durante a [fase de preparação e planeamento de pré-implantação e planeamento da rede VMware HCX.](production-ready-deployment-steps.md#vmware-hcx-network-segments)

Criará quatro perfis de rede:

   - Gestão
   - vMotion
   - Replicação
   - Uplink

1. Em **Infraestruturas**, **selecione Interconnect**  >  **Multi-Site Service Mesh**Network  >  **Profiles**Create Network  >  **Profile**.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Para cada perfil de rede, selecione a rede, o grupo portuário, forneça um nome, crie o pool IP para esse segmento específico e, em seguida, selecione **Criar**. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local.":::

Para uma visão geral deste passo, consulte a [Solução Azure VMware - VMware HCX criar](https://www.youtube.com/embed/NhyEcLco4JY) vídeo de perfil de rede.


### <a name="create-compute-profile"></a>Criar perfil computacional

1. Selecione **Perfis compute**  >  **create Compute Profile**.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Introduza um nome para o perfil e **selecione Continue**.  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Selecione os serviços para ativar, tais como migração, extensão de rede ou recuperação de desastres, e, em seguida, **selecione Continue**.
  
   > [!NOTE]
   > Geralmente, nada será mudado aqui.

1. Em **Select Service Resources**, selecione um ou mais recursos de serviço (clusters) para permitir os serviços VMware HCX selecionados.  

1. Quando vir os clusters no seu datacenter no local, selecione **Continue**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. A partir da **Select Datastore**, selecione o recurso de armazenamento de datastore para a implantação dos aparelhos VMware HCX Interconnect e, em seguida, selecione **Continue**.

   Quando vários recursos são selecionados, o VMware HCX utiliza o primeiro recurso selecionado até que a sua capacidade esteja esgotada.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. Selecione **Perfil de Rede de Gestão,** o perfil de rede de gestão que criou em etapas anteriores e, em seguida, selecione **Continue**.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

   > [!NOTE]
   > O perfil de rede de gestão permite que os aparelhos VMware HCX se comuniquem com o vCenter, podendo ser alcançados os anfitriões ESXi.

1. Selecione **Uplink Network Profile**, o perfil de rede de uplink que criou em etapas anteriores e, em seguida, selecione **Continue**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. Selecione **vMotion Network Profile**, o perfil de rede vMotion que criou em etapas anteriores e, em seguida, selecione **Continue**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. Escolha **o perfil de rede de replicação da vSphere,** o perfil de rede de replicação que criou em etapas anteriores e, em seguida, escolha **Continuar**.

   Na maioria dos casos, o perfil de rede de replicação é o mesmo que o Perfil de Rede de Gestão.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. Selecione **Switches distribuídos para extensões de rede,** os interruptores virtuais distribuídos que contêm as máquinas virtuais a migrar para a Solução VMware Azure numa rede estendida de camada 2 e, em seguida, selecione **Continue**.

   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Reveja as regras de ligação e **selecione Continuar**.  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Selecione **Acabamento** para criar o perfil de cálculo.

   Vê-se um ecrã semelhante ao mostrado abaixo.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Para uma visão geral deste passo, consulte a [Solução Azure VMware - VMware HCX criar vídeo de perfil computacional.](https://www.youtube.com/embed/qASXi5xrFzM)




### <a name="create-service-mesh"></a>Criar malha de serviço

Agora é hora de configurar a malha de serviço entre as instalações e a Azure VMware Solution SDDC.

1. Em **Infraestruturas**, selecione **Interconnect**  >  **Service Mesh**Create Service  >  **Mesh**.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Reveja os sites pré-povoados e, em seguida, **selecione Continue**. 

   >[!NOTE]
   >Se esta for a sua primeira configuração de malha de serviço, não precisará modificar este ecrã.  

1. Selecione o drop-down para os perfis de cálculo de origem e remota e, em seguida, **selecione Continue**.  

   As seleções definem os recursos onde os VMs podem consumir serviços VMware HCX.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Rever os serviços que serão ativados e, em seguida, selecione **Continue**.  

1. Em **Configuração Avançada - Os perfis de rede de uplink de override** selecionam **Continue**.  Os perfis de rede de ligação ligam-se à rede através da qual os aparelhos de interligação do site remoto podem ser alcançados.  
  
1. Em **Configuração Avançada - Escala de aparelho de extensão de rede para fora,** reveja e selecione **Continue**. 

1. Em **Configuração Avançada - Engenharia de Tráfego,** reveja e faça quaisquer modificações que se sinta necessárias e, em seguida, selecione **Continue**.

1. Reveja a pré-visualização da topologia e **selecione Continue**.

1. Introduza um nome fácil de utilizar para esta malha de serviço e **selecione Acabamento** para completar.  

1. Selecione **Ver Tarefas** para monitorizar a implementação. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local.":::

   Quando a implementação da malha de serviço estiver concluída com sucesso, verá os serviços como verdes.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Verifique a saúde da malha de serviço **Interconnect**verificando o estado do aparelho, selecione  >  **Interconnect Appliances**.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

Para uma visão geral deste passo, consulte a [Solução Azure VMware - VMware HCX criar](https://www.youtube.com/embed/FyZ0d3P_T24) vídeo de malha de serviço.



### <a name="optional-create-a-network-extension"></a>(Opcional) Criar uma extensão de rede

Se pretender estender quaisquer redes do seu ambiente no local até à Azure VMware Solution, siga estes passos para alargar essas redes.

1. Em **Serviços**, selecione **extensão de rede**e, em seguida, selecione Criar uma **extensão de rede**.

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Selecione cada uma das redes que pretende estender até Azure VMware Solution e, em seguida, selecione **Next**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local.":::

1. Introduza o GATEWAY IP no local para cada uma das redes que está a estender e, em seguida, **selecione Enviar por isso**. 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local.":::

   Leva alguns minutos para a extensão da rede estar completa. Quando o fizer, vê-se a alteração do estado para **a extensão completa**.

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Para uma visão geral deste passo, consulte o vídeo de extensão da [rede VMware VMware Azure VMware.](https://www.youtube.com/embed/cNlp0f_tTr0)


## <a name="next-steps"></a>Passos seguintes

Se tiver chegado a este ponto e o aparelho interligar o estado do túnel é **UP** e verde, pode migrar e proteger VMs de Solução VMware Azure utilizando VMware HCX.  A Azure VMware Solution suporta migrações de carga de trabalho (com ou com uma extensão de rede).  Você ainda pode fazer migrações de carga de trabalho no seu ambiente vSphere criação de redes e implantação de VMs para essas redes.  Para obter mais informações, consulte [a documentação VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) e [máquinas virtuais migratórias com VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) na documentação técnica VMware para mais detalhes sobre a utilização do HCX.
