---
title: Instalar VMware HCX
description: Confiem a solução VMware HCX para a sua nuvem privada Azure VMware Solution
ms.topic: how-to
ms.date: 09/24/2020
ms.openlocfilehash: 76a7432b78ec2141039dcdc5dd1d7572335b18e1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91263206"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>Instale hCX para solução VMware Azure

Neste artigo, percorremos procedimentos para configurar a solução VMWare HCX para a sua nuvem privada Azure VMWare Solution. O HCX permite a migração das suas cargas de trabalho VMware para a nuvem, e outros sites conectados através de vários tipos de migração suportados por HCX incorporados.

O HCX Advanced, a instalação predefinida, suporta até três ligações do local (no local ou nuvem para nuvem). Se forem necessárias mais de três ligações de site, os clientes têm a opção de ativar o addon da Empresa HCX através do Suporte, que está atualmente em pré-visualização. A HCX Enterprise tem custos adicionais para os clientes após a disponibilidade geral (GA), mas fornece [funcionalidades adicionais.](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/)


Reveja minuciosamente [Antes de começar,](#before-you-begin) [os requisitos de versão de software](#software-version-requirements)e [pré-requisitos](#prerequisites) primeiro. 

Então, vamos percorrer todos os procedimentos necessários para:

> [!div class="checklist"]
> * Implementar as instalações HCX OVA (Conector)
> * Ativar e configurar hCX
> * Configure a rede de ligação e malha de serviço
> * Configuração completa verificando o estado do aparelho

Após completar a configuração, pode seguir os próximos passos recomendados no final deste artigo.  

## <a name="before-you-begin"></a>Before you begin
    
* Reveja a [série tutorial](tutorial-network-checklist.md)básica do Software Defined Datacenter (SDDC) do Azure VMware Solution .
* Reveja e refira a [documentação VMware HCX,](https://docs.vmware.com/en/VMware-HCX/index.html) incluindo o guia do utilizador HCX.
* Rever VMware docs [Migrando Máquinas Virtuais com VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* Rever opcionalmente [as considerações de implantação de VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Analise opcionalmente materiais VMware relacionados em HCX, como a [série de blogs](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) VMware vSphere em HCX. 
* Solicite uma ativação da Azure VMware Solution HCX Enterprise através dos canais de suporte Azure VMware Solution.

Dimensionar cargas de trabalho contra recursos de computação e armazenamento é um passo de planeamento essencial quando se prepara para usar a solução Azure VMware Solution Private Cloud HCX. Aborde o passo de dimensionamento como parte do planeamento inicial do ambiente em nuvem privada. 

Também pode dimensionar cargas de trabalho completando uma Avaliação de [Solução VMware Azure](https://docs.microsoft.com/azure/migrate/how-to-create-azure-vmware-solution-assessment) no portal Azure Migrate.

## <a name="software-version-requirements"></a>Requisitos de versão de software

Os componentes da infraestrutura devem estar a executar a versão mínima necessária. 
                                                         
| Tipo de Componente    | Requisitos ambientais de origem    | Requisitos do ambiente de destino   |
| --- | --- | --- |
| vCenter Server   | 5.1<br/><br/>Se utilizar 5.5 U1 ou mais cedo, utilize a interface de utilizador HCX autónoma para operações de HCX.  | 6.0 U2 e superior   |
| ESXi   | 5.0    | ESXi 6.0 e superior   |
| NSX    | Para a extensão da rede HCX de comutadores lógicos na fonte: NSXv 6.2+ ou NSX-T 2.4+   | NSXv 6.2+ ou NSX-T 2.4+<br/><br/>Para o encaminhamento de proximidade HCX: NSXv 6.4+ (O Encaminhamento de proximidade não é suportado com NSX-T) |
| vCloud Diretor   | Não é necessário - sem interoperabilidade com vCloud Diretor no site de origem | Ao integrar o ambiente de destino com o vCloud Diretor, o mínimo é 9.1.0.2.  |

## <a name="prerequisites"></a>Pré-requisitos

* O ExpressRoute Global Reach deve ser configurado entre os circuitos SDDC ExpressRoute da Solução VMware Azure VMware.

* Todas as portas necessárias devem estar abertas entre as instalações e a Solução VMware SDDC Azure VMware (ver [documentação VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).

* Um endereço IP para O Gestor HCX no local e um mínimo de dois endereços IP para o aparelho Interconnect (IX) e Extensão de Rede (NE).

* Os aparelhos HCX IX e NE no local devem poder chegar à infraestrutura vCenter e ESXi.

* Para implantar o aparelho DE Interligação WAN, para além do bloco de endereços de rede /22 CIDR utilizado para a implantação de SDDC no portal Azure, o HCX necessita de um bloco /29. Certifique-se de que este requisito é fator no planeamento da sua rede.

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>Implementar o VMware HCX OVA no local

1. Inscreva-se no Azure VMware Solution HCX Manager na `https://x.x.x.9` porta 443 com as credenciais de utilizador **cloudadmin** e, em seguida, vá para **Suporte**.

1. Selecione o link de descarregamento para o ficheiro VMware HCX OVA. 

1. Inscreva-se no Azure VMware Solution SDDC vCenter e selecione **HCX**.
   
1. Vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-template.png" alt-text="Em seguida, vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local.":::

1. Selecione um nome e localização e, em seguida, selecione um recurso/cluster onde o HCX precisa de ser implantado. Em seguida, rever detalhes e recursos necessários.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-template.png" alt-text=" Selecione um nome e localização e, em seguida, selecione um recurso/cluster onde o HCX precisa de ser implantado. Em seguida, rever detalhes e recursos necessários.":::

1. Reveja os termos da licença e, se concordar, selecione o armazenamento e a rede necessários. e selecione **Seguinte**.

1. No **modelo Personalizar,** insira todas as informações necessárias. 

   :::image type="content" source="media/hybrid-cloud-extension-installation/customize-template.png" alt-text="No modelo De Personalização, insira todas as informações necessárias.":::

1. Selecione **Seguinte**, verifique a configuração e selecione **Finish** para implementar HCX OVA.

## <a name="activate-hcx"></a>Ativar HCX

Após a instalação, execute os seguintes passos.

1. Inicie sessão no gestor de HCX no local `https://HCXManagerIP:9443` e inicie sessão com as credenciais de nome de utilizador de **administração.** 

   > [!IMPORTANT]
   > Certifique-se de incluir o número de `9443` porta com o endereço IP do HCX Manager.

1. Em **Licenciamento,** insira a sua **Tecla Avançada HCX**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-key.png" alt-text="Em Licenciamento, insira a sua Chave Avançada HCX.":::
    
    > [!NOTE]
    > O HCX Manager deve ter acesso à internet aberto ou um representante configurado.

1. Em **vCenter,** se necessário, edite a informação vCenter.

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-vcenter.png" alt-text="No vCenter, se necessário, edite a informação vCenter.":::

1. Na **Localização do Datacenter,** se necessário, edite a localização do datacenter.

   :::image type="content" source="media/hybrid-cloud-extension-installation/system-location.png" alt-text="Na Localização do Datacenter, se necessário, edite a localização do datacenter.":::

## <a name="configure-hcx"></a>Configure HCX 

1. Inscreva-se no local vCenter, e em **Casa,** selecione **HCX**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-vcenter.png" alt-text="Inscreva-se no vCenter no local e em Casa, selecione HCX.":::

1. Em **Infraestruturas**, selecione **Emparelhamento de Sítio**Adicionar um  >  **emparelhamento de site**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/site-pairing.png" alt-text="Em Infraestruturas, selecione Site Pairing > Adicione um emparelhamento do site.":::

1. Introduza o endereço de UTILIZADOR ou IP remoto HCX, Azure VMware Solution cloudadmin username e password e, em seguida, selecione **Connect**.

   O sistema mostra o local ligado.

   :::image type="content" source="media/hybrid-cloud-extension-installation/site-connection.png" alt-text="O sistema mostra o local ligado.":::

1. Em **Infraestruturas**, **selecione Interconnect**  >  **Multi-Site Service Mesh**Network  >  **Profiles**Create Network  >  **Profile**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/create-network-profile.png" alt-text="Em Infraestruturas, selecione Interconnect > rede de serviço multi-local > perfis de rede > criar perfil de rede.":::

1. Para o novo perfil de rede, insira as gamas de endereços HCX IX e NE IP (é necessário um mínimo de dois endereços IP para aparelhos IX e NE).

   :::image type="content" source="media/hybrid-cloud-extension-installation/enter-address-ranges.png" alt-text="Para o novo perfil de rede, insira as gamas de endereços HCX IX e NE IP (é necessário um mínimo de dois endereços IP para aparelhos IX e NE).":::
  
   > [!NOTE]
   > O aparelho de extensão de rede (HCX-NE) tem uma relação de um para um com um interruptor virtual distribuído (DVS).  

1. Selecione **perfil de computação**  >  **Crie o perfil de computação**.

1. Introduza um nome de perfil computacional e **selecione Continue**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/create-compute-profile.png" alt-text="Introduza um nome de perfil de computação e selecione Continue.":::

1. Selecione os serviços para ativar, tais como migração, extensão de rede ou recuperação de desastres, e, em seguida, **selecione Continue**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-services.png" alt-text="Selecione os serviços para ativar, como migração, extensão de rede ou recuperação de desastres, e, em seguida, selecione Continue.":::

1. Em **Select Service Resources**, selecione um ou mais recursos de serviço para os quais os serviços HCX selecionados devem ser ativados. **Selecione Continuar**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-service-resources.png" alt-text="Em Select Service Resources, selecione um ou mais recursos de serviço para os quais os serviços HCX selecionados devem ser ativados. Selecione Continuar.":::
  
   > [!NOTE]
   > Selecione clusters específicos em que os VMs de origem são direcionados para a migração usando HCX.

1. Selecione **Datastore** e **selecione Continue**. 
      
   Selecione cada recurso de cálculo e armazenamento para a implantação dos aparelhos de interligação HCX. Quando vários recursos são selecionados, o HCX utiliza o primeiro recurso selecionado até que a sua capacidade seja esgotada.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/deployment-resources.png" alt-text="Selecione cada recurso de cálculo e armazenamento para a implantação dos aparelhos de interligação HCX. Quando vários recursos são selecionados, o HCX utiliza o primeiro recurso selecionado até que a sua capacidade seja esgotada.":::

1. Selecione o perfil de rede de gestão criado nos **Perfis de Rede** e selecione **Continue**.  
      
   Selecione o perfil de rede através do qual a interface de gestão do vCenter e dos anfitriões ESXi podem ser alcançados. Se ainda não definiu tal perfil de rede, pode criá-lo aqui.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/management-network-profile.png" alt-text="Selecione o perfil de rede através do qual a interface de gestão do vCenter e dos anfitriões ESXi podem ser alcançados. Se ainda não definiu tal perfil de rede, pode criá-lo aqui.":::

1. Selecione **Network Uplink** e selecione **Continue**.
      
   Selecione um ou mais perfis de rede para que um dos seguintes seja verdadeiro:  
   * Os Aparelhos interligados no local remoto podem ser alcançados através desta rede  
   * Os aparelhos de lado remoto podem chegar aos aparelhos interligados locais através desta rede.  
    
   Se tiver redes ponto a ponto como o Direct Connect que não são partilhadas em vários sites, pode saltar este passo, uma vez que os perfis de computação são partilhados com vários sites. Nesses casos, os perfis da Rede Uplink podem ser ultrapassados e especificados durante a criação da malha de serviço de interligação.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/uplink-network-profile.png" alt-text="Selecione Network Uplink e selecione Continue.":::

1. Selecione **vMotion Network Profile** e selecione **Continue**.
      
   Selecione o perfil de rede através do qual a interface vMotion dos anfitriões ESXi pode ser alcançada. Se ainda não definiu tal perfil de rede, pode criá-lo aqui. Se não tiver rede vMotion, selecione **Perfil de Rede de Gestão**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/vmotion-network-profile.png" alt-text="Selecione o perfil de rede vMotion e selecione Continue.":::

1. A partir do **perfil de rede de replicação vSphere,** selecione um perfil de rede a interface de replicação vSphere dos anfitriões ESXi e, em seguida, selecione **Continue**.
      
   Na maioria dos casos, este perfil é o mesmo que o Perfil de Rede de Gestão.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png" alt-text="A partir do perfil de rede de replicação vSphere, selecione um perfil de rede da interface de replicação vSphere dos anfitriões ESXi e, em seguida, selecione Continue.":::

1. A partir de **Switches Distribuídos Selecionados para Extensões de Rede**, selecione os DVS nos quais tem redes os VMs que serão integrados e estão ligados.  **Selecione Continuar**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/distributed-switches.png" alt-text="A partir de Switches Distribuídos Selecionados para Extensões de Rede, selecione os DVS nos quais tem redes os VMs que serão integrados e estão ligados.  Selecione Continuar.":::

1. Reveja as regras de ligação e **selecione Continuar**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/complete-compute-profile.png" alt-text="Reveja as regras de ligação e selecione Continue.":::

1.  Selecione **Acabamento** para criar o perfil de cálculo.

## <a name="configure-network-uplink"></a>Configurar a rede Uplink

Agora configurar a mudança de perfil de rede no Azure VMware Solution SDDC para network uplink.

1. Inscreva-se no SDDC NSX-T para criar um novo interruptor lógico, ou utilize um interruptor lógico existente que pode ser usado para uplink de rede entre as instalações e Azure VMware Solution SDDC.

1. Crie um perfil de rede para uplink HCX em Azure VMware Solution SDDC que pode ser usado para comunicações SDDC de Solução VMware Azure VMware.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/network-profile-uplink.png" alt-text="Crie um perfil de rede para uplink HCX em Azure VMware Solution SDDC que pode ser usado para comunicações SDDC de Solução VMware Azure VMware.":::

1. Introduza um nome para o perfil de rede e pelo menos 4-5 endereços IP gratuitos com base na extensão de rede L2 necessária.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-uplink-profile.png" alt-text="Introduza um nome para o perfil de rede e pelo menos 4-5 endereços IP gratuitos com base na extensão de rede L2 necessária.":::

1. Selecione **Criar** para completar a configuração SDDC SDDC da Solução VMware Azure

## <a name="configure-service-mesh"></a>Rede de serviço de configuração

Agora configure a malha de serviço entre as instalações e a Azure VMware Solution SDDC.

1. Inscreva-se no Azure VMware Solution SDDC vCenter e selecione **HCX**.

2. Em **Infraestruturas**, selecione **Interconnect**  >  **Service Mesh**Create Service  >  **Mesh** para configurar a rede e computar perfis criados em etapas anteriores.    

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-service-mesh.png" alt-text="Em Infraestruturas, selecione Interconnect > Service Mesh > Criar Malha de Serviço para configurar a rede e os perfis de cálculo criados em etapas anteriores.":::

3. Selecione sites emparelhados para ativar a capacidade híbrida e **selecione Continue**.   

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-paired-sites.png" alt-text="Selecione sites emparelhados para ativar a capacidade híbrida e selecione Continue.":::

4. Selecione os perfis de cálculo de origem e remota para permitir serviços de hibrididade e selecione **Continue**.
      
   As seleções definem os recursos, onde os VMs podem consumir serviços HCX.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/enable-hybridity.png" alt-text="As seleções definem os recursos, onde os VMs podem consumir serviços HCX.":::

5. Selecione serviços para ativar e selecionar **Continue**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-services.png" alt-text="Selecione serviços para ativar e selecionar Continue.":::

6. Em **Configuração Avançada - Os perfis de rede de uplink de override** selecionam **Continue**.  
      
   Os perfis de rede de ligação são utilizados para ligar à rede através da qual os aparelhos de interligação do site remoto podem ser alcançados.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/override-uplink-profiles.png" alt-text="Os perfis de rede de ligação são utilizados para ligar à rede através da qual os aparelhos de interligação do site remoto podem ser alcançados.":::

7. Selecione **Configurar a escala do aparelho de extensão de rede para fora**. 

   :::image type="content" source="media/hybrid-cloud-extension-installation/network-extension-scale-out.png" alt-text="Selecione Configurar a escala do aparelho de extensão de rede para fora.":::

8. Introduza a contagem do aparelho correspondente à contagem do interruptor DVS.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/appliance-scale.png" alt-text="Introduza a contagem do aparelho correspondente à contagem do interruptor DVS.":::

9. **Selecione Continue** a saltar.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/traffic-engineering.png" alt-text="Selecione Continue a saltar.":::

10. Reveja a pré-visualização da topologia e **selecione Continue**. 

11. Introduza um nome fácil de utilizar para esta malha de serviço e **selecione Terminar** para completar.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/complete-service-mesh.png" alt-text="Malha de serviço completa":::

   A malha de serviço está implantada e configurada.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/deployed-service-mesh.png" alt-text="Malha de serviço implantada":::

## <a name="check-appliance-status"></a>Verifique o estado do aparelho
Para verificar o estado do **Interconnect**aparelho, selecione  >  **Interligação .** 

:::image type="content" source="media/hybrid-cloud-extension-installation/appliance-status.png" alt-text="Verifique o estado do aparelho.":::

## <a name="next-steps"></a>Passos seguintes

Quando o aparelho interligar o **estado do túnel** é **UP** e verde, está pronto para migrar e proteger VMs de Solução VMware Azure utilizando HCX. Consulte [a documentação VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) e [máquinas virtuais migratórias com VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) na documentação técnica VMware.
