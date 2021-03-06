---
title: Tutorial - Implementar e configurar VMware HCX
description: Aprenda a implementar e configurar uma solução VMware HCX para a sua nuvem privada Azure VMware Solution.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 9ee451ce4be59c51299d66e4042ed344026100cf
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011005"
---
# <a name="deploy-and-configure-vmware-hcx"></a>Implementar e configurar o VMware HCX

Este artigo mostra-lhe como implantar e configurar o Conector VMware HCX no local para a sua nuvem privada Azure VMware Solution. Com o VMware HCX, pode migrar as suas cargas de trabalho VMware para Azure VMware Solution e outros sites conectados através de vários tipos de migração. Uma vez que a Azure VMware Solution implementa e configura o HCX Cloud Manager, tem de descarregar, ativar e configurar o Conector HCX no centro de dados VMware nas suas instalações.

O VMware HCX Advanced Connector está pré-implantado na Solução VMware Azure. Suporta até três ligações do local (no local para nuvem, ou nuvem para nuvem). Se precisar de mais de três ligações de site, envie um pedido de [suporte](https://portal.azure.com/#create/Microsoft.Support) para ativar o addon [VMware HCX Enterprise.](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/)  

>[!TIP]
>Embora a ferramenta VMware Configuration Maximum descreva os pares máximos do site para ser de 25 entre o Conector no local e o Cloud Manager, o licenciamento limita-o a três para HCX Advanced e 10 para HCX Enterprise Edition.

>[!NOTE]
>A VMware HCX Enterprise está disponível com a Azure VMware Solution como um serviço de pré-visualização. É gratuito e está sujeito a termos e condições para um serviço de pré-visualização. Depois de o serviço VMware HCX Enterprise estar geralmente disponível, receberá um aviso de 30 dias de que a faturação irá mudar. Também terá a opção de desligar ou excluir o serviço. A redução da HCx Enterprise para a HCX Advanced é possível sem a recolocação, mas terás de registar um bilhete de apoio para essa ação ter lugar. Se planear uma degradação, certifique-se de que não estão agendadas migrações e ou características como RAV, MON não estão a ser utilizadas.

Primeiro, reveja [Antes de começar,](#before-you-begin) [os requisitos de versão de software](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)e os [Pré-requisitos](#prerequisites). 

Então, vamos percorrer todos os procedimentos necessários para:

> [!div class="checklist"]
> * Descarregue o VMware HCX Connector OVA.
> * Implementar no local VMware HCX OVA (VMware HCX Connector).
> * Ativar o Conector VMware HCX.
> * Emparelhe o conector VMware HCX no local com o seu Azure VMware Solution HCX Cloud Manager.
> * Configure a interligação (perfil de rede, perfil de cálculo e malha de serviço).
> * Configuração completa, verificando o estado do aparelho e validando a possibilidade de migração.

Depois de terminar, siga os próximos passos recomendados no final deste artigo.  

## <a name="before-you-begin"></a>Antes de começar

Ao preparar a sua implementação, recomendamos que reveja a seguinte documentação VMware:

* [Guia de utilizador VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)
* [Máquinas virtuais migratórias com VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [Considerações de implantação de VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* [VMware blog series - migração em nuvem](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) 
* [Portas de rede necessárias para VMware HCX](https://ports.vmware.com/home/VMware-HCX)


## <a name="prerequisites"></a>Pré-requisitos

Se planeia utilizar a VMware HCX Enterprise, certifique-se de que solicitou a ativação através dos canais de suporte Azure VMware Solution.


### <a name="on-premises-vsphere-environment"></a>Ambiente vSphere no local

Certifique-se de que o seu ambiente vSphere (ambiente de origem) satisfaz os [requisitos mínimos.](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html) 

### <a name="network-and-ports"></a>Rede e portas

* [O Azure ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md) está configurado entre os circuitos expressRoute em nuvem privada Azure VMware Solution.

* [Todas as portas necessárias](https://ports.vmware.com/home/VMware-HCX) estão abertas para comunicação entre componentes no local e Azure VMware Solution privada.

### <a name="ip-addresses"></a>Endereços IP

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="download-the-vmware-hcx-connector-ova"></a>Descarregue o VMware HCX Connector OVA

Antes de colocar o aparelho virtual no seu vCenter no local, tem de descarregar o VMware HCX Connector OVA.  

1. No portal Azure, selecione a nuvem privada Azure VMware Solution. 

1. **Selecione Gerir a**  >  **Conectividade** e selecione o separador **HCX** para identificar o endereço IP do Gestor IP do Azure VMware Solution HCX Manager. 

   :::image type="content" source="media/tutorial-vmware-hcx/find-hcx-ip-address.png" alt-text="Screenshot do endereço IP VMware HCX." lightbox="media/tutorial-vmware-hcx/find-hcx-ip-address.png":::

1. Selecione **Gerir**  >  **identidade**. 

   Os URLs e as credenciais de utilizador para o visor privado de vCenter e NSX-T Manager.

   > [!TIP]
   > A palavra-passe vCenter foi definida quando configura a nuvem privada. É a mesma palavra-passe que vai usar para iniciar sposição no Azure VMware Solution HCX Manager. Pode selecionar **Gerar uma nova palavra-passe** para gerar novas palavras-passe vCenter e NSX-T.

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Exiba urls e credenciais de cloud vCenter e NSX Manager." border="true":::


1. Abra uma janela do navegador, inscreva-se no Azure VMware Solution HCX Manager na `https://x.x.x.9` porta 443 com as credenciais de utilizador **cloudadmin \@ vsphere.local**

1. Selecione   >  **Atualizações do sistema de administração** e, em seguida, selecione **Request Download Link**.

1. Selecione a opção à sua escolha para descarregar o ficheiro VMware HCX Connector OVA.

## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>Implementar o Conector VMware HCX OVA no local

1. No seu vCenter no local, selecione um [modelo OVF](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) para implementar o Conector VMware HCX no seu vCenter no local. 

   > [!TIP]
   > Selecione o ficheiro OVA que descarregou na secção anterior.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Screenshot de navegação para um modelo OVF." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Selecione um nome e localização e selecione um recurso ou cluster onde está a implementar o Conector VMware HCX. Em seguida, reveja os detalhes e os recursos necessários e selecione **Next**.  

1. Rever os termos da licença. Se estiver de acordo, selecione o armazenamento e a rede necessários e, em seguida, selecione **Seguinte**.

1. Selecione o armazenamento e selecione **Next**.

1. Selecione o segmento de rede de gestão VMware HCX que previamente definiu na secção [de pré-requisitos de endereços IP.](#ip-addresses)  Em seguida, selecione **Seguinte**.

1. No **modelo Personalizar,** insira todas as informações necessárias e, em seguida, selecione **Next**. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Screenshot das caixas para personalizar um modelo." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Verifique a configuração e, em seguida, **selecione Acabamento** para implementar o Conector VMware HCX OVA.
   
   > [!IMPORTANT]
   > Terá de ligar o aparelho virtual manualmente.  Depois de ligar, aguarde 10-15 minutos antes de avançar para o passo seguinte.

Para obter uma visão geral deste procedimento, consulte a [Solução Azure VMware: HCX Appliance Deployment](https://www.youtube.com/embed/UKmSTYrL6AY) Video. 


## <a name="activate-vmware-hcx"></a>Ativar VMware HCX

Depois de colocar o VMware HCX Connector OVA no local e ligar o aparelho, está pronto a ser ativado. Primeiro, precisa de obter uma chave de licença do portal Azure VMware Solution.

1. No portal Azure VMware Solution, vá para **Gerir**  >  conectividade, selecione o separador **HCX** e, em seguida, selecione **Adicionar**.

1. Utilize as credenciais **de administração** para iniciar singing no local VMware HCX Manager em `https://HCXManagerIP:9443` . 

   > [!TIP]
   > Definiu a palavra-passe do utilizador **administrativo** durante a implementação do ficheiro OVA do VMware HCX Manager.

   > [!IMPORTANT]
   > Certifique-se de incluir o número de `9443` porta com o endereço IP VMware HCX Manager.

1. Em **Licenciamento,** insira a sua chave para **a Tecla Avançada HCX** e selecione **Ative**.  
   
    > [!NOTE]
    > VMware HCX Manager deve ter acesso à internet aberto ou um proxy configurado.

1. Na **Localização do Datacenter,** forneça a localização mais próxima para instalar o VMware HCX Manager no local. Em seguida, selecione **Continuar**.

1. No **Nome do Sistema,** modifique o nome ou aceite o predefinido e selecione **Continue**.
   
1. Selecione **Sim, Continue**.

1. Em **Connect your vCenter**, forneça o endereço FQDN ou IP do seu servidor vCenter e as credenciais apropriadas e, em seguida, selecione **Continue**.
   
   > [!TIP]
   > O servidor vCenter é onde implementou o Conector VMware HCX no seu datacenter.

1. Em **Configurar SSO/PSC,** forneça o endereço FQDN ou IP do seu Controlador de Serviços de Plataforma e, em seguida, selecione **Continue**.
   
   > [!NOTE]
   > Normalmente, é o mesmo que o seu endereço FQDN ou IP do vCenter.

1. Verifique se as informações inseridas estão corretas e **selecione Restart**.
    
   > [!NOTE]
   > Vai sentir um atraso depois de reiniciar antes de ser solicitado para o próximo passo.

Após o reinício dos serviços, verá o vCenter a mostrar-se como verde no ecrã que aparece. Tanto o vCenter como o SSO devem ter os parâmetros de configuração adequados, que devem ser os mesmos que o ecrã anterior.

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Screenshot do painel de instrumentos com o estado do vCenter verde." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Para uma visão geral deste procedimento, consulte a [Solução Azure VMware: Ativar](https://www.youtube.com/embed/PnVg6SZkQsY?rel=0&amp;vq=hd720) o vídeo HCX.

   > [!IMPORTANT]
   > Quer esteja a utilizar a HCX Advanced ou a HCX Enterprise, poderá ter de instalar o patch a partir do [artigo KB 81558](https://kb.vmware.com/s/article/81558)da VMware. 

## <a name="configure-the-vmware-hcx-connector"></a>Configure o Conector VMware HCX

Agora está pronto para adicionar um emparelhamento de site, criar uma rede e perfil de computação, e ativar serviços como migração, extensão de rede ou recuperação de desastres. 

### <a name="add-a-site-pairing"></a>Adicione um emparelhamento de site

Pode ligar ou emparelhar o VMware HCX Cloud Manager em Azure VMware Solution com o Conector VMware HCX no seu datacenter. 

1. Inscreva-se no seu vCenter no local, e em **Casa,** selecione **HCX**.

1. Em **Infraestruturas**, selecione **O Emparelhamento do Site** e, em seguida, selecione a opção **'Ligar ao Local Remoto'** (no meio do ecrã). 

1. Introduza o URL ou endereço IP do Azure VMware Solution HCX Cloud Manager que observou anteriormente , o nome `https://x.x.x.9` de utilizador Azure VMware Solution cloudadmin \@ vsphere.local username e a palavra-passe. Em seguida, selecione **Ligar**.

   > [!NOTE]
   > Para estabelecer com sucesso um par de site:
   > * O seu Conector VMware HCX deve ser capaz de encaminhar para o seu HCX Cloud Manager IP sobre a porta 443.
   >
   > * Use a mesma palavra-passe que usou para iniciar súm no vCenter. Definiu esta palavra-passe no ecrã inicial de implantação.

   Verá um ecrã que mostra que o seu VMware HCX Cloud Manager na Azure VMware Solution e o seu Conector VMware HCX no local estão ligados (emparelhado).

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Screenshot que mostra o emparelhamento do HCX Manager em Azure VMware Solution e o VMware HCX Connector.":::

Para uma visão geral deste procedimento, consulte a [Solução Azure VMware: HCX Site Pairing.](https://www.youtube.com/embed/jXOmYUnbWZY?rel=0&amp;vq=hd720)

### <a name="create-network-profiles"></a>Criar perfis de rede

O VMware HCX Connector implementa um subconjunto de aparelhos virtuais (automatizados) que requerem vários segmentos IP. Ao criar os seus perfis de rede, utiliza os segmentos IP identificados durante a [fase de preparação e planeamento de pré-implantação e planeamento da rede VMware HCX](production-ready-deployment-steps.md#vmware-hcx-network-segments).

Criará quatro perfis de rede:

   - Gestão
   - vMotion
   - Replicação
   - Uplink

1. Em **Infraestruturas**, **selecione Interconnect**  >  **Multi-Site Service Mesh** Network  >  **Profiles** Create Network  >  **Profile**.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Screenshot de seleções para começar a criar um perfil de rede." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Para cada perfil de rede, selecione a rede e o grupo portuário, forneça um nome e crie o pool IP do segmento. Em seguida, selecione **Criar**. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Screenshot de detalhes para um novo perfil de rede.":::

Para uma visão geral deste procedimento, consulte a [Solução Azure VMware: HCX Network Profile](https://www.youtube.com/embed/O0rU4jtXUxc) video.


### <a name="create-a-compute-profile"></a>Criar um perfil computacional

1. Em **Infraestruturas**, selecione **Interconnect**  >  **Compute Profiles**  >  **Create Compute Profile**.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Screenshot que mostra as seleções para começar a criar um perfil computacional." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Introduza um nome para o perfil e **selecione Continue**.  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Screenshot que mostra a entrada de um nome de perfil computacional e o botão Continuar." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Selecione os serviços para permitir, tais como migração, extensão de rede ou recuperação de desastres, e, em seguida, **selecione Continue**.
  
   > [!NOTE]
   > Geralmente, nada muda aqui.

1. Em **Select Service Resources**, selecione um ou mais recursos de serviço (clusters) para ativar os serviços VMware HCX selecionados.  

1. Quando vir os clusters no seu datacenter no local, selecione **Continue**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Screenshot que mostra recursos de serviço selecionados e o botão Continuar." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. A partir da **Select Datastore**, selecione o recurso de armazenamento de datastore para a implantação dos aparelhos VMware HCX Interconnect. Em seguida, selecione **Continuar**.

   Quando vários recursos são selecionados, o VMware HCX utiliza o primeiro recurso selecionado até que a sua capacidade esteja esgotada.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Screenshot que mostra um recurso de armazenamento de dados selecionado e o botão Continuar." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. A partir do **Perfil de Rede de Gestão Select,** selecione o perfil de rede de gestão que criou em etapas anteriores. Em seguida, selecione **Continuar**.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Screenshot que mostra a seleção de um perfil de rede de gestão e o botão Continuar." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

1. A partir do **Perfil de Rede Select Uplink**, selecione o perfil de rede de uplink que criou no procedimento anterior. Em seguida, selecione **Continuar**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Screenshot que mostra a seleção de um perfil de rede de ligação uplink e o botão Continuar." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. A partir do **Perfil de Rede vMotion,** selecione o perfil de rede vMotion que criou em etapas anteriores. Em seguida, selecione **Continuar**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Screenshot que mostra a seleção de um perfil de rede vMotion e o botão Continuar." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. A partir do **perfil de rede de replicação vSphere,** selecione o perfil de rede de replicação que criou em etapas anteriores. Em seguida, selecione **Continuar**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Screenshot que mostra a seleção de um perfil de rede de replicação e o botão Continuar." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. A partir de **Switches Distribuídos Selecionados para Extensões de Rede**, selecione os interruptores que contêm as máquinas virtuais a migrar para a Solução VMware Azure numa rede estendida em camada 2. Em seguida, selecione **Continuar**.

   > [!NOTE]
   > Se não estiver a migrar máquinas virtuais em redes estendidas camadas 2 (L2), pode saltar este passo.
   
   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Screenshot que mostra a seleção de interruptores virtuais distribuídos e o botão Continuar." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Reveja as regras de ligação e **selecione Continuar**.  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Screenshot que mostra as regras de ligação e o botão Continuar." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Selecione **Acabamento** para criar o perfil de cálculo.


   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Screenshot que mostra informações de perfil computacional." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Para uma visão geral deste procedimento, consulte o [vídeo Azure VMware Solution: Compute Profile.](https://www.youtube.com/embed/e02hsChI3b8)

### <a name="create-a-service-mesh"></a>Criar uma malha de serviço

Agora é hora de configurar uma malha de serviço entre as instalações e a nuvem privada Azure VMware Solution.



> [!NOTE]
> Para estabelecer com sucesso uma malha de serviço com a Solução Azure VMware:
>
> * As portas UDP 500/4500 estão abertas entre os endereços de perfil de rede 'uplink' do VMware HCX Connector VMware E os endereços de perfil de rede 'uplink' da Solução VMware HCX Cloud da Azure.
>
> * Certifique-se de rever as [portas HCX necessárias vMware](https://ports.vmware.com/home/VMware-HCX).

1. Em **Infraestruturas**, selecione **Interconnect**  >  **Service Mesh** Create Service  >  **Mesh**.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Screenshot das seleções para começar a criar uma malha de serviço." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Reveja os sites pré-povoados e, em seguida, **selecione Continue**. 

   > [!NOTE]
   > Se esta for a sua primeira configuração de malha de serviço, não precisará modificar este ecrã.  

1. Selecione os perfis de cálculo de origem e remotas das listas de drop-down e, em seguida, **selecione Continue**.  

   As seleções definem os recursos onde os VMs podem consumir serviços VMware HCX.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Screenshot que mostra a seleção do perfil de computação de origem." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Screenshot que mostra a seleção do perfil de computação remota." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Rever os serviços que serão ativados e, em seguida, selecionar **Continue**.  

1. Em **Configuração Avançada - Override Uplink Network profiles**, selecione **Continue**.  

   Os perfis de rede de ligação ligam-se à rede através da qual os aparelhos de interligação do site remoto podem ser alcançados.  
  
1. Em **Configuração Avançada - Escala de aparelho de extensão de rede para fora,** reveja e selecione **Continue**. 

   Pode ter até oito VLANs por aparelho, mas pode utilizar outro aparelho para adicionar mais oito VLANs. Você também deve ter espaço IP para explicar os aparelhos adicionais, e é um IP por aparelho.  Para obter mais informações, consulte [os limites de configuração do VMware HCX](https://configmax.vmware.com/guest?vmwareproduct=VMware%20HCX&release=VMware%20HCX&categories=41-0,42-0,43-0,44-0,45-0).
   
   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png" alt-text="Screenshot que mostra onde aumentar a contagem de VLAN." lightbox="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png":::

1. Em **Configuração Avançada - Engenharia de Tráfego,** reveja e faça quaisquer modificações que assentes que assesse ser necessárias e, em seguida, selecione **Continue**.

1. Reveja a pré-visualização da topologia e **selecione Continue**.

1. Introduza um nome fácil de utilizar para esta malha de serviço e **selecione Acabamento** para completar.  

1. Selecione **Ver Tarefas** para monitorizar a implementação. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Screenshot que mostra o botão para visualizar tarefas.":::

   Quando a implementação da malha de serviço terminar com sucesso, verá os serviços como verdes.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Screenshot que mostra indicadores verdes nos serviços." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Verifique a saúde da malha de serviço verificando o estado do aparelho. 

1. Selecione   >  **Interligar Aparelhos**.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Screenshot que mostra seleções para verificar o estado do aparelho." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

Para uma visão geral deste procedimento, consulte a [Solução Azure VMware: Vídeo de malha](https://www.youtube.com/embed/COY3oIws108) de serviço.

### <a name="optional-create-a-network-extension"></a>(Opcional) Criar uma extensão de rede

Se pretender estender quaisquer redes do seu ambiente no local até à Azure VMware Solution, siga estes passos:

1. Em **Serviços**, selecione **Extensão de Rede** Crie uma  >  **extensão de rede**.

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Screenshot que mostra seleções para começar a criar uma extensão de rede." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Selecione cada uma das redes que pretende estender até Azure VMware Solution e, em seguida, selecione **Next**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Screenshot que mostra a seleção de uma rede.":::

1. Introduza o GATEWAY IP no local para cada uma das redes que está a estender e, em seguida, **selecione Enviar por isso**. 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Screenshot que mostra a entrada de um endereço IP gateway.":::

   Leva alguns minutos para a extensão da rede terminar. Quando o fizer, vê-se a alteração do estado para **extensão completada**.

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Screenshot que mostra o estado da extensão completa." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Para uma visão geral deste procedimento, consulte a [Solução Azure VMware: Vídeo de extensão de rede.](https://www.youtube.com/embed/gYR0nftKui0)


## <a name="next-steps"></a>Passos seguintes

Se o estado do túnel de interligação HCX for **UP** e verde, pode migrar e proteger VMs de Solução VMware Azure utilizando VMware HCX. A Azure VMware Solution suporta migrações de carga de trabalho (com ou sem extensão de rede). Você ainda pode migrar cargas de trabalho no seu ambiente vSphere, juntamente com a criação no local de redes e implantação de VMs para essas redes.  

Para obter mais informações sobre a utilização do HCX, aceda à documentação técnica da VMware:

* [Documentação VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html)
* [Máquinas virtuais migratórias com VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [Portas necessárias de HCX](https://ports.vmware.com/home/VMware-HCX)
* [Crie um servidor de procuração HCX antes de aprovar a chave de licença](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-920242B3-71A3-4B24-9ACF-B20345244AB2.html?hWord=N4IghgNiBcIA4CcD2APAngAgBIGEAaIAvkA)
