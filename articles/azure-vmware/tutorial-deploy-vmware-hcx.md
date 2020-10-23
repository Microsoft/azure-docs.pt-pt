---
title: Tutorial - Implementar e configurar VMware HCX
description: Aprenda a implementar e configurar uma solução VMware HCX para a sua nuvem privada Azure VMware Solution.
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: c78eae11497702054bb54b5980228fd0a3962577
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367776"
---
# <a name="deploy-and-configure-vmware-hcx"></a>Implementar e configurar o VMware HCX

Neste artigo, percorremos os procedimentos para implementar e configurar o Conector VMware HCX para a sua nuvem privada Azure VMware Solution. Com o VMware HCX, pode migrar as suas cargas de trabalho VMware para Azure VMware Solution e outros sites conectados através de vários tipos de migração. Uma vez que a Azure VMware Solution implementa e configura o HCX Cloud Manager, tem de descarregar, ativar e configurar o Conector HCX no centro de dados VMware nas suas instalações.

O VMware HCX Advanced Connector está pré-implantado na Solução VMware Azure. Suporta até três ligações do local (no local para nuvem, ou nuvem para nuvem). Se precisar de mais de três ligações de site, envie um pedido de [suporte](https://rc.portal.azure.com/#create/Microsoft.Support) para ativar o addon [VMware HCX Enterprise.](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) O addon está atualmente em pré-visualização. 

>[!NOTE]
>VMware HCX Enterprise Edition (EE) está disponível com a Azure VMware Solution como um serviço de pré-visualização. É gratuito e está sujeito a termos e condições para um serviço de pré-visualização. Depois do serviço VMware HCX EE estar geralmente disponível, receberá um aviso de 30 dias de que a faturação irá mudar. Também terá a opção de desligar ou optar por sair do serviço.

Em primeiro lugar, reveja minuciosamente as secções [Antes de começar,](#before-you-begin) [os requisitos](#software-version-requirements)da versão do Software e as secções [Pré-requisitos](#prerequisites) deste artigo. 

Então, vamos percorrer todos os procedimentos necessários para:

> [!div class="checklist"]
> * Implementar as VMware HCX OVA (Conector HCX).
> * Ativar o Conector VMware HCX.
> * Emparelhe o seu Conector HCX no local com o seu Azure VMware Solution HCX Cloud Manager.
> * Configure a interligação (perfil de rede, perfil de cálculo e malha de serviço).
> * Configuração completa, verificando o estado do aparelho e validando a possibilidade de migração.

Depois de terminar, pode seguir os próximos passos recomendados no final deste artigo.  

## <a name="before-you-begin"></a>Antes de começar
   
* Reveja a base da Solução Azure VMware Software-Defined [séries tutoriais](tutorial-network-checklist.md)datacenter (SDDC).
* Reveja e refira a [documentação VMware HCX,](https://docs.vmware.com/en/VMware-HCX/index.html)incluindo o guia do utilizador HCX.
* [Reveja máquinas virtuais migratórias com VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) em VMware Docs.
* Rever opcionalmente [as considerações de implantação de VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Analise opcionalmente materiais VMware relacionados em HCX, como a [série de blogs](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html)VMware vSphere . 
* Opcionalmente solicite uma ativação Azure VMware Solution HCX Enterprise através de canais de suporte Azure VMware Solution.
* Rever opcionalmente [as portas de rede necessárias para o HCX](https://ports.vmware.com/home/VMware-HCX).
* Embora o Azure VMware Solution HCX Cloud Manager venha pré-configurado a partir da rede /22 que está fornecida para a nuvem privada Azure VMware Solution, o conector HCX no local requer que aloque gamas de rede a partir da sua rede no local. Estas redes e gamas são descritas mais tarde neste artigo.

Dimensionar cargas de trabalho contra os recursos de computação e armazenamento é um passo essencial de planeamento. Aborde o passo de dimensionamento como parte do planeamento inicial para um ambiente de nuvem privada. 

Pode dimensionar cargas de trabalho completando uma [avaliação da Solução VMware Azure](../migrate/how-to-create-azure-vmware-solution-assessment.md) no portal Azure Migrate.

## <a name="software-version-requirements"></a>Requisitos de versão de software

Os componentes da infraestrutura devem estar a executar a versão mínima necessária. 
                                                         
| Tipo de componente    | Requisitos ambientais de origem    | Requisitos de ambiente de destino   |
| --- | --- | --- |
| vCenter Server   | 5.1<br/><br/>Se estiver a utilizar 5.5 U1 ou mais cedo, utilize a interface de utilizador HCX autónoma para operações HCX.  | 6.0 U2 e mais tarde   |
| ESXi   | 5.0    | ESXi 6.0 e mais tarde   |
| NSX    | Para a extensão da rede HCX de comutadores lógicos na fonte: NSXv 6.2+ ou NSX-T 2.4+.   | NSXv 6.2+ ou NSX-T 2.4+<br/><br/>Para o Encaminhamento de Proximidade HCX: NSXv 6.4+. (O Encaminhamento de Proximidade não é suportado com NSX-T.) |
| vCloud Diretor   | Não é necessário. Não há interoperabilidade com o diretor do vCloud no site de origem. | Quando se integra o ambiente de destino com o vCloud Diretor, o mínimo é 9.1.0.2.  |

## <a name="prerequisites"></a>Pré-requisitos

### <a name="network-and-ports"></a>Rede e portas

* Configure [Azure ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md) entre os circuitos on-in e Azure VMware Solution SDDC ExpressRoute.

* [Todas as portas necessárias](https://ports.vmware.com/home/VMware-HCX) devem estar abertas para comunicação entre os componentes no local e o Azure VMware Solution SDDC.

Para mais informações, consulte a [documentação VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html).


### <a name="ip-addresses"></a>Endereços IP

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>Implementar o Conector VMware HCX OVA no local

> [!NOTE]
> Antes de colocar o aparelho virtual no seu vCenter no local, tem de descarregar o VMware HCX Connector OVA. 

1. Abra uma janela do navegador, inscreva-se no Azure VMware Solution HCX Manager na `https://x.x.x.9` porta 443 com as credenciais de utilizador **cloudadmin** e, em seguida, vá para **suporte**.

   > [!TIP]
   > Note o endereço IP do HCX Cloud Manager na Solução VMware Azure. Para identificar o endereço IP, no painel Azure VMware Solution, vá para **Gerir**conectividade  >  **Connectivity** e, em seguida, selecione o separador **HCX.** 
   >
   >A palavra-passe vCenter foi definida quando configura a nuvem privada.

1. Selecione o link **de descarregamento** para descarregar o ficheiro VMware HCX Connector OVA.

1. Vá ao seu vCenter no local. Selecione um modelo OVF, que é o ficheiro OVA que descarregou, para implantar o Conector HCX no seu vCenter no local.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Screenshot de navegação para um modelo OVF." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Selecione um nome e localização e selecione um recurso/cluster onde está a implementar o Conector HCX. Em seguida, reveja os detalhes e os recursos necessários.  

   :::image type="content" source="media/tutorial-vmware-hcx/configure-template.png" alt-text="Screenshot de navegação para um modelo OVF." lightbox="media/tutorial-vmware-hcx/configure-template.png":::

1. Rever os termos da licença. Se estiver de acordo, selecione o armazenamento e a rede necessários e, em seguida, selecione **Seguinte**.

1. No **modelo Personalizar,** insira todas as informações necessárias. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Screenshot de navegação para um modelo OVF." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Selecione **Seguinte**, verifique a configuração e, em seguida, selecione **Finish** para implementar o Conector HCX OVA.
     
   > [!NOTE]
   > Geralmente, o Conector VMware HCX que está a implementar agora é implantado na rede de gestão do cluster.  
   
   > [!IMPORTANT]
   > Pode ser necessário ligar o aparelho virtual manualmente.  Se for esse o caso, aguarde 10-15 minutos antes de seguir para o próximo passo.

Para obter uma visão geral deste procedimento, consulte a [Solução Azure VMware: HCX Appliance Deployment](https://www.youtube.com/embed/BwSnQeefnso) Video. 


## <a name="activate-vmware-hcx"></a>Ativar VMware HCX

Depois de colocar o VMware HCX Connector OVA no local e ligar o aparelho, está pronto a ser ativado. Primeiro, precisa de obter uma chave de licença do portal Azure VMware Solution.

1. No portal Azure VMware Solution, vá para **Gerir**  >  **Connectivity**conectividade, selecione o separador **HCX** e, em seguida, selecione **Adicionar**.

1. Utilize as credenciais **de administração** para iniciar singing no local VMware HCX Manager em `https://HCXManagerIP:9443` . 

   > [!IMPORTANT]
   > Certifique-se de incluir o número de `9443` porta com o endereço IP VMware HCX Manager.

1. Em **Licenciamento,** insira a sua chave para **a Tecla Avançada HCX**.  
   
    > [!NOTE]
    > VMware HCX Manager deve ter acesso à internet aberto ou um proxy configurado.

1. Na **Localização do Datacenter,** forneça a localização mais próxima para instalar o VMware HCX Manager no local.

1. No **Nome do Sistema,** modifique o nome ou aceite o padrão.
   
1. Selecione **Sim, Continue**.
    
1. Em **Connect your vCenter**, forneça o endereço FQDN ou IP do seu servidor vCenter e as credenciais apropriadas e, em seguida, selecione **Continue**.
   
1. Em **Configurar SSO/PSC,** forneça o endereço FQDN ou IP do seu Controlador de Serviços de Plataforma e, em seguida, selecione **Continue**.
   
   >[!NOTE]
   >Normalmente, esta entrada é a mesma que o seu endereço FQDN ou IP do vCenter.

1. Verifique se todas as entradas estão corretas e selecione **Restart**.
    
   > [!NOTE]
   > Vai sentir um atraso depois de reiniciar antes de ser solicitado para o próximo passo.

Após o reinício dos serviços, é fundamental que veja o vCenter a mostrar-se como verde no ecrã que aparece. Tanto o vCenter como o SSO devem ter os parâmetros de configuração adequados, que devem ser os mesmos que o ecrã anterior.

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Screenshot de navegação para um modelo OVF." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Para uma visão geral deste procedimento, consulte a [Solução Azure VMware: Ativar](https://www.youtube.com/embed/BkAV_TNYxdE) o vídeo HCX.


## <a name="configure-the-vmware-hcx-connector"></a>Configure o Conector VMware HCX

Agora está pronto para adicionar um emparelhamento de site, criar uma rede e perfil de computação, e ativar serviços como migração, extensão de rede ou recuperação de desastres. 

### <a name="add-a-site-pairing"></a>Adicione um emparelhamento de site

Pode ligar (emparelhamento) o VMware HCX Cloud Manager na Solução VMware Azure com o Conector VMware HCX no seu datacenter. 

1. Inscreva-se no seu vCenter no local, e em **Casa,** selecione **HCX**.

   :::image type="content" source="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png" alt-text="Screenshot de navegação para um modelo OVF." lightbox="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png":::

1. Em **Infraestruturas**, selecione **O Emparelhamento do Site**e, em seguida, selecione a opção **'Ligar ao Local Remoto'** (no meio do ecrã). 

   :::image type="content" source="media/tutorial-vmware-hcx/connect-remote-site.png" alt-text="Screenshot de navegação para um modelo OVF." lightbox="media/tutorial-vmware-hcx/connect-remote-site.png":::

1. Introduza o endereço de URL ou IP remoto hCX que observou anteriormente, o nome de utilizador Azure VMware Solution cloudadmin@vsphere.local e a palavra-passe. Em seguida, selecione **Ligar**.

   > [!NOTE]
   > Para estabelecer um par de site com sucesso, o seu conector HCX deve ser capaz de encaminhar para o seu HCX Cloud Manager IP sobre a porta 443.
   >
   > A palavra-passe é a mesma palavra-passe que usou para iniciar súm no vCenter. Definiu esta palavra-passe no ecrã inicial de implantação.

   Vê um ecrã que mostra que o seu HCX Cloud Manager na Solução VMware Azure e os seus conectores HCX no local estão ligados (emparelhado).

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Screenshot de navegação para um modelo OVF.":::

Para uma visão geral deste procedimento, consulte a [Solução Azure VMware: HCX Site Pairing.](https://www.youtube.com/embed/sKizDCRHOko)



### <a name="create-network-profiles"></a>Criar perfis de rede

O VMware HCX implementa um subconjunto de aparelhos virtuais (automatizados) que requerem vários segmentos IP. Ao criar os seus perfis de rede, define os segmentos IP que identificou durante a [fase de preparação e planeamento de pré-implantação e planeamento da rede VMware HCX.](production-ready-deployment-steps.md#vmware-hcx-network-segments)

Criará quatro perfis de rede:

   - Gestão
   - vMotion
   - Replicação
   - Uplink

1. Em **Infraestruturas**, **selecione Interconnect**  >  **Multi-Site Service Mesh**Network  >  **Profiles**Create Network  >  **Profile**.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Screenshot de navegação para um modelo OVF." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Para cada perfil de rede, selecione a rede e o grupo portuário, forneça um nome e crie o pool IP para esse segmento. Em seguida, selecione **Criar**. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Screenshot de navegação para um modelo OVF.":::

Para uma visão geral deste procedimento, consulte a [Solução Azure VMware: HCX Network Profile](https://www.youtube.com/embed/NhyEcLco4JY) video.


### <a name="create-a-compute-profile"></a>Criar um perfil computacional

1. Selecione **Perfis compute**  >  **create Compute Profile**.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Screenshot de navegação para um modelo OVF." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Introduza um nome para o perfil e **selecione Continue**.  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Screenshot de navegação para um modelo OVF." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Selecione os serviços para permitir, tais como migração, extensão de rede ou recuperação de desastres, e, em seguida, **selecione Continue**.
  
   > [!NOTE]
   > Geralmente, nada muda aqui.

1. Em **Select Service Resources**, selecione um ou mais recursos de serviço (clusters) para ativar os serviços VMware HCX selecionados.  

1. Quando vir os clusters no seu datacenter no local, selecione **Continue**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Screenshot de navegação para um modelo OVF." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. A partir da **Select Datastore**, selecione o recurso de armazenamento de datastore para a implantação dos aparelhos VMware HCX Interconnect. Em seguida, selecione **Continuar**.

   Quando vários recursos são selecionados, o VMware HCX utiliza o primeiro recurso selecionado até que a sua capacidade esteja esgotada.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Screenshot de navegação para um modelo OVF." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. A partir do **Perfil de Rede de Gestão Select,** selecione o perfil de rede de gestão que criou em etapas anteriores. Em seguida, selecione **Continuar**.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Screenshot de navegação para um modelo OVF." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

   > [!NOTE]
   > O perfil de rede de gestão permite que os eletrodomésticos VMware HCX se comuniquem com o vCenter. Os anfitriões ESXi podem ser alcançados através deste perfil.

1. A partir do **Perfil de Rede Select Uplink**, selecione o perfil de rede de uplink que criou em etapas anteriores. Em seguida, selecione **Continuar**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Screenshot de navegação para um modelo OVF." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. A partir do **Perfil de Rede vMotion,** selecione o perfil de rede vMotion que criou em etapas anteriores. Em seguida, selecione **Continuar**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Screenshot de navegação para um modelo OVF." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. A partir do **perfil de rede de replicação vSphere,** selecione o perfil de rede de replicação que criou em etapas anteriores. Em seguida, selecione **Continuar**.

   Na maioria dos casos, o perfil de rede de replicação é o mesmo que o perfil da rede de gestão.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Screenshot de navegação para um modelo OVF." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. A partir de **Switches Distribuídos Selecionados para Extensões de Rede**, selecione os interruptores virtuais distribuídos que contêm as máquinas virtuais a migrar para Azure VMware Solution numa rede estendida camada 2. Em seguida, selecione **Continuar**.

   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Screenshot de navegação para um modelo OVF." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Reveja as regras de ligação e **selecione Continuar**.  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Screenshot de navegação para um modelo OVF." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Selecione **Acabamento** para criar o perfil de cálculo.


   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Screenshot de navegação para um modelo OVF." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Para uma visão geral deste procedimento, consulte o [vídeo Azure VMware Solution: Compute Profile.](https://www.youtube.com/embed/qASXi5xrFzM)

### <a name="create-a-service-mesh"></a>Criar uma malha de serviço

Agora é hora de configurar uma malha de serviço entre as instalações e a Azure VMware Solution SDDC.

   > [!NOTE]
   > Para estabelecer com sucesso uma malha de serviço com a Solução Azure VMware:
   >
   > As portas UDP 500/4500 estão abertas entre os endereços de perfil de rede definidos pelo conector HCX no local e os endereços de perfil de rede 'uplink' da Solução VMware Azure VCX Cloud.
   >
   > Certifique-se de rever as portas necessárias ao [HCX](https://ports.vmware.com/home/VMware-HCX).

1. Em **Infraestruturas**, selecione **Interconnect**  >  **Service Mesh**Create Service  >  **Mesh**.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Screenshot de navegação para um modelo OVF." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Reveja os sites pré-povoados e, em seguida, **selecione Continue**. 

   >[!NOTE]
   >Se esta for a sua primeira configuração de malha de serviço, não precisará modificar este ecrã.  

1. Selecione os perfis de cálculo de origem e remotas das listas de drop-down e, em seguida, **selecione Continue**.  

   As seleções definem os recursos onde os VMs podem consumir serviços VMware HCX.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Screenshot de navegação para um modelo OVF." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Screenshot de navegação para um modelo OVF." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Rever os serviços que serão ativados e, em seguida, selecionar **Continue**.  

1. Em **Configuração Avançada - Override Uplink Network profiles**, selecione **Continue**.  

   Os perfis de rede de ligação ligam-se à rede através da qual os aparelhos de interligação do site remoto podem ser alcançados.  
  
1. Em **Configuração Avançada - Escala de aparelho de extensão de rede para fora,** reveja e selecione **Continue**. 

1. Em **Configuração Avançada - Engenharia de Tráfego,** reveja e faça quaisquer modificações que assentes que assesse ser necessárias e, em seguida, selecione **Continue**.

1. Reveja a pré-visualização da topologia e **selecione Continue**.

1. Introduza um nome fácil de utilizar para esta malha de serviço e **selecione Acabamento** para completar.  

1. Selecione **Ver Tarefas** para monitorizar a implementação. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Screenshot de navegação para um modelo OVF.":::

   Quando a implementação da malha de serviço terminar com sucesso, verá os serviços como verdes.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Screenshot de navegação para um modelo OVF." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Verifique a saúde da malha de serviço verificando o estado do aparelho. 
1. Selecione **Interconnect**  >  **Interligar Aparelhos**.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Screenshot de navegação para um modelo OVF." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

Para uma visão geral deste procedimento, consulte a [Solução Azure VMware: Vídeo de malha](https://www.youtube.com/embed/FyZ0d3P_T24) de serviço.



### <a name="optional-create-a-network-extension"></a>(Opcional) Criar uma extensão de rede

Se pretender estender quaisquer redes do seu ambiente no local até à Azure VMware Solution, siga estes passos:

1. Em **Serviços**, selecione **extensão de rede**e, em seguida, selecione Criar uma **extensão de rede**.

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Screenshot de navegação para um modelo OVF." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Selecione cada uma das redes que pretende estender até Azure VMware Solution e, em seguida, selecione **Next**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Screenshot de navegação para um modelo OVF.":::

1. Introduza o GATEWAY IP no local para cada uma das redes que está a estender e, em seguida, **selecione Enviar por isso**. 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Screenshot de navegação para um modelo OVF.":::

   Leva alguns minutos para a extensão da rede terminar. Quando o fizer, vê-se a alteração do estado para **extensão completada**.

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Screenshot de navegação para um modelo OVF." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Para uma visão geral deste passo, consulte a [Solução Azure VMware: Vídeo de extensão de rede.](https://www.youtube.com/embed/cNlp0f_tTr0)


## <a name="next-steps"></a>Passos seguintes

Se tiver chegado a este ponto e o estado do túnel de interligação do aparelho for **UP** e verde, pode migrar e proteger VMs de Solução VMware Azure utilizando VMware HCX. A Azure VMware Solution suporta migrações de carga de trabalho (com ou sem extensão de rede). Você ainda pode migrar cargas de trabalho no seu ambiente vSphere, juntamente com a criação no local de redes e implantação de VMs para essas redes.  

Para obter mais informações sobre a utilização do HCX, aceda à documentação técnica da VMware:

* [Documentação VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html)
* [Máquinas virtuais migratórias com VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* [Portas necessárias de HCX](https://ports.vmware.com/home/VMware-HCX)
