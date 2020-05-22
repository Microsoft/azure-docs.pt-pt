---
title: Instale extensão de nuvem híbrida (HCX)
description: Configurar a solução VMWare Hybrid Cloud Extension (HCX) para a sua nuvem privada Azure VMWare Solution (AVS)
ms.topic: how-to
ms.date: 05/19/2020
ms.openlocfilehash: 74bc0dece2fc565aebc6e5301ef7a36187d6a250
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83776476"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>Instale HCX para solução Azure VMWare

Neste artigo, passamos por procedimentos para configurar a solução VMWare Hybrid Cloud Extension (HCX) para a sua nuvem privada Azure VMWare Solution (AVS). O HCX Advanced (instalação predefinida) suporta até três locais externos, onde cada site externo requer que um gestor ou conector da EMPRESA HCX seja instalado e ativado.
O HCX permite a migração das suas cargas de trabalho VMware para a nuvem e ou outros sites conectados através de vários tipos de migração apoiados por HCX incorporados. Se forem necessários mais de três sites, os clientes têm a opção de ativar o addon HCX Enterprise através do Suporte. A HCX Enterprise transporta encargos adicionais para os clientes após a disponibilidade geral (GA), mas fornece [funcionalidades adicionais](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/).

Reveja cuidadosamente Antes de [começar,](#before-you-begin) [requisitos](#software-version-requirements)de versão de software e [pré-requisitos](#prerequisites) primeiro. 

Depois, passamos por todos os procedimentos necessários para:

> [!div class="checklist"]
> * Implementar o HCX OVA no local
> * Ativar e configurar O HCX
> * Configure a rede de uplink e malha de serviço
> * Complete a configuração verificando o estado do aparelho

Após a conclusão da configuração, são fornecidos os próximos passos recomendados.

## <a name="before-you-begin"></a>Antes de começar
    
* Reveja a [série tutorial](tutorial-network-checklist.md) básica do Software AVS Defined Datacenter (SDDC)
* Reveja e refira a [documentação VMware HCX,](https://docs.vmware.com/en/VMware-HCX/index.html) incluindo o guia de utilizador HCX
* Rever Os docs VMware [migrando máquinas virtuais com VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* Revisão opcional [das considerações de implementação do VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* Reveja opcionalmente materiais VMware relacionados com o HCX, como a série de [blogs](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) VMware vSphere no HCX. 
* Encomende uma ativação AVS HCX Enterprise através dos canais de suporte AVS.

Dimensionar cargas de trabalho contra os recursos de cálculo e armazenamento é um passo essencial de planeamento ao preparar-se para usar a solução AVS Private Cloud HCX. Este passo de tamanho deve ser abordado como parte do planeamento inicial do ambiente em nuvem privada. 

## <a name="software-version-requirements"></a>Requisitos de versão de software
Os componentes de infraestrutura devem estar a executar a versão mínima necessária. 
                                                         
| Tipo de Componente                                                          | Requisitos ambientais de origem                                                                   | Requisitos ambientais de destino                                                                      |
| --- | --- | --- |
| vCenter Server                                                          | 5.1<br/><br/>Se utilizar 5.5 U1 ou mais cedo, utilize a interface de utilizador AUTÓNOMA DO HCX para operações HCX.         | 6.0 U2 e acima                                                                                          |
| ESXi                                                                    | 5.0                                                                                               | ESXi 6.0 e acima                                                                                        |
| NSX                                                                     | Para a extensão da rede HCX de interruptores lógicos na Fonte: NSXv 6.2+ ou NSX-T 2.4+              | NSXv 6.2+ ou NSX-T 2.4+<br/><br/For HCX Closey Routing: NSXv 6.4+ (Encaminhamento de proximidade não suportado com NSX-T) |
| diretor vCloud                                                         | Não é necessário - sem interoperabilidade com o Diretor vCloud no site de origem | Quando o ambiente de destino é integrado com o VCloud Diretor, o mínimo é de 9.1.0.2.              |

## <a name="prerequisites"></a>Pré-requisitos

* O alcance global deve ser configurado entre os circuitos AVS SDDC ER.

* Todas as portas necessárias devem estar abertas entre as instalações e o AVS SDDC (ver [documentação VMWare HCX).](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)

* Um endereço IP para hcx manager no local e um mínimo de dois endereços IP para o aparelho Interconnect (IX) e Extensão de Rede (NE).

* Os aparelhos HCX IX e NE devem poder atingir a infraestrutura vCenter e ESXi.

* Para implantar o aparelho WAN Interconnect, para além do bloco de endereços de rede 1.22 CIDR utilizado para a implantação de SDDC no portal Azure, o HCX necessita de um bloco /29. Devias ter isso em conta no planeamento da tua rede.

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>Implementar o VMware HCX OVA no local

1. Inscreva-se no VCenter AVS SDDC e selecione **HCX**.

    ![Selecione HCX no VCenter AVS](./media/hybrid-cloud-extension-installation/avs-vsphere-client.png)

1. Para descarregar o ficheiro VMware HCX OVA, selecione **Administration**  >  **System Updates**.

    ![Obter atualizações do sistema](./media/hybrid-cloud-extension-installation/administration-updates.png)

1. Selecione um modelo OVF para implantar no local vCenter.  

    ![Selecione modelo OVF](./media/hybrid-cloud-extension-installation/select-template.png)

1. Selecione um nome e localização e, em seguida, selecione um recurso/cluster onde o HCX precisa de ser implantado. Em seguida, reveja os detalhes e os recursos necessários.  

    ![Detalhes do modelo de revisão](./media/hybrid-cloud-extension-installation/configure-template.png)

1. Reveja os termos da licença e, se concordar, selecione o armazenamento e a rede necessários. Em seguida, selecione **Seguinte**.

1. No **modelo Personalizar,** introduza todas as informações necessárias. 

    ![Modelo de personalizar](./media/hybrid-cloud-extension-installation/customize-template.png)  

1. Selecione **Seguinte,** verifique a configuração e selecione **Finish** para implementar HCX OVA.

## <a name="activate-hcx"></a>Ativar o HCX

Após a instalação, execute os seguintes passos.

1. Abra o HCX Manager `https://HCXManagerIP:9443` e inscreva-se com o seu nome de utilizador e a sua palavra-passe. 

1. Em **Licenciamento,** introduza a chave **avançada HCX**.  

    ![Introduza a tecla HCX](./media/hybrid-cloud-extension-installation/hcx-key.png)  
    
    > [!NOTE]
    > O HCX Manager deve ter acesso à Internet aberto ou um proxy configurado.

1. Configure vCenter.

    ![Configure VCenter](./media/hybrid-cloud-extension-installation/configure-vcenter.png)

1. Na **Localização datacenter,** se necessário, edite a localização do datacenter.

    ![Localização da base de dados](./media/hybrid-cloud-extension-installation/system-location.png)

## <a name="configure-hcx"></a>Configure HCX 

1. Inscreva-se no vCenter no local e, em seguida, selecione **Home**  >  **HCX**.

    ![HCX no VCenter](./media/hybrid-cloud-extension-installation/hcx-vcenter.png)

1. Selecione O emparelhamento do site **de infraestrutura**  >  **Site Pairing**  >  **Adicione um emparelhamento**de site .

    ![Adicionar emparelhamento de site](./media/hybrid-cloud-extension-installation/site-pairing.png)

1. Introduza URL **HCX remoto,** **nome de utilizador**e **palavra-passe.** Em seguida, selecione **Ligar**.

   O sistema mostra o site conectado.
   
    ![Ligação ao local](./media/hybrid-cloud-extension-installation/site-connection.png)

1. Selecione **Interconnect**  >  **Multi-Site Service Mesh**Network  >  **Profiles**Create Network  >  **Profile**.

    ![Criar o perfil da rede](./media/hybrid-cloud-extension-installation/create-network-profile.png)

1. Introduza as gamas de endereços IP HCX IX e NE (é necessário um mínimo de 2 endereços IP para aparelhos IX e NE).
    
   ![Insira as gamas de endereços IP](./media/hybrid-cloud-extension-installation/enter-address-ranges.png)
  
   > [!NOTE]
   > O aparelho de extensão de rede (HCX-NE) tem uma relação um-para-um com um interruptor virtual distribuído (DVS).  

1. Agora selecione **O perfil Compute**  >  **Criar o perfil de cálculo**.

1. Introduza um nome de perfil computacional e selecione **Continuar**.  

    ![Criar perfil computacional](./media/hybrid-cloud-extension-installation/create-compute-profile.png)

1. Selecione serviços para permitir como migração, extensão da rede, recuperação de desastres pr. Selecione **Continuar**.

    ![Selecione serviços](./media/hybrid-cloud-extension-installation/select-services.png)

1. Em **Select Service Resources,** selecione um ou mais recursos de serviço para os quais os serviços HCX selecionados devem ser ativados. Selecione **Continuar**.
    
   ![Selecione recursos de serviço](./media/hybrid-cloud-extension-installation/select-service-resources.png)
  
   > [!NOTE]
   > Selecione clusters específicos em que os VMs de origem são direcionados para a migração usando HCX.

1. Selecione **Datastore** e selecione **Continuar**. 
      
    Selecione cada recurso de cálculo e armazenamento para a implementação dos aparelhos HCX Interconnect. Quando são selecionados vários recursos, o HCX utiliza o primeiro recurso selecionado até que a sua capacidade esteja esgotada.  
    
    ![Selecione recursos de implantação](./media/hybrid-cloud-extension-installation/deployment-resources.png)

1. Selecione o perfil de rede de gestão criado nos Perfis de **Rede** e selecione **Continuar**.  
      
    Selecione o perfil de rede através do qual a interface de gestão dos anfitriões vCenter e ESXi pode ser alcançada. Se ainda não definiu tal perfil de rede, pode criá-lo aqui.  
    
    ![Selecione o perfil da rede de gestão](./media/hybrid-cloud-extension-installation/management-network-profile.png)

1. Selecione **Network Uplink** e selecione **Continue**.
      
    Selecione um ou mais perfis de rede de modo a que um dos seguintes perfis seja verdade:  
    * Os Eletrodomésticos Interconnect no local remoto podem ser alcançados através desta rede  
    * Os aparelhos laterais remotos podem chegar aos eletrodomésticos interligados locais através desta rede.  
    
    Se tiver redes ponto-a-ponto como o Direct Connect que não são partilhadas em vários sites, pode saltar este passo, uma vez que os perfis de computação são partilhados com vários sites. Nesses casos, os perfis da Rede Uplink podem ser ultrapassados e especificados durante a criação da malha interligada Service.  
    
    ![Selecione o perfil da Rede Uplink](./media/hybrid-cloud-extension-installation/uplink-network-profile.png)

1. Selecione **o perfil da rede vMotion** e selecione **Continuar**.
      
    Selecione o perfil de rede através do qual a interface vMotion dos anfitriões ESXi pode ser alcançada. Se ainda não definiu tal perfil de rede, pode criá-lo aqui. Se não tiver rede vMotion, selecione **Management Network Profile**.  
    
    ![Selecione o perfil da rede vMotion](./media/hybrid-cloud-extension-installation/vmotion-network-profile.png)

1. Selecione **vSphere Replication Network Profile** e selecione **Continue**.
      
    Selecione um Perfil de Rede através do qual a interface de replicação vSphere dos anfitriões ESXi pode ser alcançada. Na maioria dos casos, este perfil é o mesmo que o Perfil da Rede de Gestão.  
    
    ![Selecione o perfil da Rede de Replicação vSphere](./media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png)

1. Selecione **Interruptores Distribuídos para extensões de rede** e selecione **Continuar**.  
      
    Selecione os Interruptores Virtuais Distribuídos em que tem redes às quais estão ligadas as Máquinas Virtuais que serão migradas.

    ![Selecione Interruptores Virtuais Distribuídos](./media/hybrid-cloud-extension-installation/distributed-switches.png)

1. Reveja as regras de ligação e selecione **Continue**. Selecione **Terminar** para criar o perfil de computação.  

    ![Criar perfil computacional](./media/hybrid-cloud-extension-installation/complete-compute-profile.png)

## <a name="configure-network-uplink"></a>Configure Network Uplink

Agora configure a alteração do perfil de rede no AVS SDDC para Network Uplink.

1. Inscreva-se no SDDC NSX-T para criar um novo interruptor lógico, ou utilize um interruptor lógico existente que pode ser usado para network uplink entre as instalações e o SDDC AVS.

1. Crie um perfil de rede para o uplink HCX no AVS SDDC que pode ser usado para a comunicação AVS SDDC.  
    
   ![Criar perfil de rede para uplink](./media/hybrid-cloud-extension-installation/network-profile-uplink.png)

1. Introduza um nome para o perfil de rede e pelo menos 4-5 endereços IP gratuitos com base na extensão da rede L2 necessária.  
    
   ![Configurar o perfil de rede para uplink](./media/hybrid-cloud-extension-installation/configure-uplink-profile.png)

1. Selecione **Criar** para completar a configuração AVS SDDC

## <a name="configure-service-mesh"></a>Configurar malha de serviço

Agora configure a malha de serviço entre as instalações e a AVS SDDC.

1. Inscreva-se no VCenter AVS SDDC e selecione **HCX**.

1. Selecione **Infraestrutura**  >  **Interconnect**  >  **Service Mesh**Criar malha de  >  **serviço**.  Configure os perfis de rede e cálculo criados em etapas anteriores.    
      
    ![Configurar malha de serviço](./media/hybrid-cloud-extension-installation/configure-service-mesh.png)

1. Selecione **Criar malha** de serviço e selecione **Continuar**.  
      
    Selecione locais emparelhados entre os quais permitir a mobilidade híbrida.  
    
    ![Selecione sites emparelhados](./media/hybrid-cloud-extension-installation/select-paired-sites.png)

1. Selecione **o perfil Compute** e selecione **Continuar**.
      
    Selecione um perfil de computação cada um na fonte e sites remotos para ativar serviços de hibrididade. As seleções vão definir os recursos, onde as Máquinas Virtuais poderão consumir os serviços HCX.  
      
    ![Ativar serviços de hibrididade](./media/hybrid-cloud-extension-installation/enable-hybridity.png)

1. Selecione serviços a ativar para HCX e selecione **Continue**.  
      
    ![Selecione serviços HCX](./media/hybrid-cloud-extension-installation/hcx-services.png)

1. Em **Configuração Avançada - Sobrepor os perfis da Rede Uplink** selecione **Continue**.  
      
    Os perfis de rede uplink são utilizados para ligar à rede através da qual os aparelhos de interligação do site remoto podem ser alcançados.  
      
    ![Anular perfis de uplink](./media/hybrid-cloud-extension-installation/override-uplink-profiles.png)

1. Na **configuração avançada – Escala de aparelho de extensão de rede para fora,** selecione configurar a escala do aparelho de **extensão da rede para fora**. 
      
    ![Escala de extensão de rede para fora](./media/hybrid-cloud-extension-installation/network-extension-scale-out.png)

1. Introduza a contagem do aparelho correspondente à contagem de interruptores DVS.  
      
    ![Configure a contagem de aparelhos](./media/hybrid-cloud-extension-installation/appliance-scale.png)

1. Em **Configuração Avançada - Engenharia de Tráfego,** selecione **Continuar**.  
      
    ![Configurar a engenharia de tráfego](./media/hybrid-cloud-extension-installation/traffic-engineering.png)

1. Reveja a pré-visualização de topologia e selecione **Continue**. Em seguida, introduza um nome fácil de utilizar para esta Malha de Serviço e selecione **Finish** para completar.  
      
    ![Malha de serviço completa](./media/hybrid-cloud-extension-installation/complete-service-mesh.png)

    A Malha de serviço está implantada e configurada.  
      
    ![Malha de serviço implantada](./media/hybrid-cloud-extension-installation/deployed-service-mesh.png)

## <a name="check-appliance-status"></a>Verifique o estado do aparelho
Para verificar o estado do aparelho, selecione **Aparelhos Interconnect**  >  **Appliances**. 
      
![Estado do aparelho](./media/hybrid-cloud-extension-installation/appliance-status.png)

## <a name="next-steps"></a>Passos seguintes

Quando o aparelho interliga o Estado do **Túnel** estiver **ligado** e verde, está pronto para migrar e proteger os VMs AVS utilizando HCX. Consulte a [documentação do VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) e [as Máquinas Virtuais Migratórias com o VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) na documentação técnica da VMWare.
