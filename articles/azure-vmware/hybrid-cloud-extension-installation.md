---
title: Instalar extensão de nuvem híbrida (HCX)
description: Confiem a solução VMware Hybrid Cloud Extension (HCX) para a sua nuvem privada Azure VMware Solution (AVS)
ms.topic: how-to
ms.date: 05/19/2020
ms.openlocfilehash: 3037d12ebbb036098cfc00a42521513bc2df6170
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85367551"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>Instale hCX para solução VMware Azure

Neste artigo, percorremos procedimentos para configurar a solução VMWare Hybrid Cloud Extension (HCX) para a sua nuvem privada Azure VMWare Solution (AVS). O HCX permite a migração das suas cargas de trabalho VMware para a nuvem, e outros sites conectados através de vários tipos de migração suportados por HCX incorporados.

HCX Advanced, a instalação predefinida, suporta até três locais externos. Se forem necessários mais de três sites, os clientes têm a opção de ativar o add-on da Empresa HCX através do Suporte. A instalação da HCX Enterprise tem custos adicionais para os clientes após a disponibilidade geral (GA), mas fornece [funcionalidades adicionais.](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/)


Reveja minuciosamente [Antes de começar,](#before-you-begin) [os requisitos de versão de software](#software-version-requirements)e [pré-requisitos](#prerequisites) primeiro. 

Depois, passamos por todos os procedimentos necessários para:

> [!div class="checklist"]
> * Implementar as instalações HCX OVA
> * Ativar e configurar hCX
> * Configure a rede de ligação e malha de serviço
> * Configuração completa verificando o estado do aparelho

Após completar a configuração, pode seguir os próximos passos recomendados no final deste artigo.  

## <a name="before-you-begin"></a>Before you begin
    
* Reveja a [série tutorial](tutorial-network-checklist.md) básica do AVS Software Defined Datacenter (SDDC)
* Rever e fazer referência à [documentação VMware HCX,](https://docs.vmware.com/en/VMware-HCX/index.html) incluindo o guia do utilizador HCX
* Rever VMware docs [Migrando Máquinas Virtuais com VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* Rever opcionalmente [considerações de implementação de VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* Analise opcionalmente materiais VMware relacionados em HCX, como a [série de blogs](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) VMware vSphere em HCX. 
* Encomende uma ativação AVS HCX Enterprise através de canais de suporte AVS.

Dimensionar cargas de trabalho contra os recursos de computação e armazenamento é um passo de planeamento essencial quando se prepara para usar a solução VS Private Cloud HCX. Aborde o passo de dimensionamento como parte do planeamento inicial do ambiente em nuvem privada.   

## <a name="software-version-requirements"></a>Requisitos de versão de software
Os componentes da infraestrutura devem estar a executar a versão mínima necessária. 
                                                         
| Tipo de Componente    | Requisitos ambientais de origem    | Requisitos do ambiente de destino   |
| --- | --- | --- |
| vCenter Server   | 5.1<br/><br/>Se utilizar 5.5 U1 ou mais cedo, utilize a interface de utilizador HCX autónoma para operações de HCX.  | 6.0 U2 e superior   |
| ESXi   | 5.0    | ESXi 6.0 e superior   |
| NSX    | Para extensão de rede HCX de switches lógicos na fonte: NSXv 6.2+ ou NSX-T 2.4+   | NSXv 6.2+ ou NSX-T 2.4+<br/><br/>Para o encaminhamento de proximidade HCX: NSXv 6.4+ (Encaminhamento de proximidade não suportado com NSX-T) |
| vCloud Diretor   | Não é necessário - sem interoperabilidade com vCloud Diretor no site de origem | Ao integrar o ambiente de destino com o vCloud Diretor, o mínimo é 9.1.0.2.  |

## <a name="prerequisites"></a>Pré-requisitos

* O alcance global deve ser configurado entre os circuitos ER AVS SDDC.

* Todas as portas necessárias devem estar abertas entre as instalações e a AVS SDDC (ver [documentação VMware HCX).](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)

* Um endereço IP para O Gestor HCX no local e um mínimo de dois endereços IP para o aparelho Interconnect (IX) e Extensão de Rede (NE).

* Os aparelhos HCX IX e NE no local devem poder chegar à infraestrutura vCenter e ESXi.

* Para implantar o aparelho DE Interligação WAN, para além do bloco de endereços de rede /22 CIDR utilizado para a implantação de SDDC no portal Azure, o HCX necessita de um bloco /29. Certifique-se de que este requisito é fator no planeamento da sua rede.

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>Implementar o VMware HCX OVA no local

1. Inscreva-se no AVS SDDC vCenter e selecione **HCX**.

    ![Selecione HCX para AVS vCenter](./media/hybrid-cloud-extension-installation/avs-vsphere-client.png)

1. Em **Administração**, selecione **'Actualizar' do Sistema** e, em seguida, selecione **Solicite o link de descarregamento** para descarregar o ficheiro VMware HCX OVA.

    ![Obter atualizações do sistema](./media/hybrid-cloud-extension-installation/administration-updates.png)

1. Em seguida, vá ao vCenter no local e selecione um modelo OVF para implementar no seu vCenter no local.  

    ![Selecione o modelo OVF](./media/hybrid-cloud-extension-installation/select-template.png)

1. Selecione um nome e localização e, em seguida, selecione um recurso/cluster onde o HCX precisa de ser implantado. Em seguida, rever detalhes e recursos necessários.  

    ![Rever detalhes do modelo](./media/hybrid-cloud-extension-installation/configure-template.png)

1. Reveja os termos da licença e, se concordar, selecione o armazenamento e a rede necessários. Em seguida, selecione **Seguinte**.

1. No **modelo Personalizar,** insira todas as informações necessárias. 

    ![Modelo de personalização](./media/hybrid-cloud-extension-installation/customize-template.png)  

1. Selecione **Seguinte**, verifique a configuração e selecione **Finish** para implementar HCX OVA.

## <a name="activate-hcx"></a>Ativar HCX

Após a instalação, execute os seguintes passos.

1. Inicie sessão no gestor HCX no local `https://HCXManagerIP:9443` e inicie sessão com o seu nome de utilizador e a sua palavra-passe. 

   > [!IMPORTANT]
   > Certifique-se de incluir o número de `9443` porta com o endereço IP do HCX Manager.

1. Em **Licenciamento,** insira a sua **Tecla Avançada HCX**.  

    ![Insira a tecla HCX](./media/hybrid-cloud-extension-installation/hcx-key.png)  
    
    > [!NOTE]
    > O HCX Manager deve ter acesso à internet aberto ou um representante configurado.

1. Em **vCenter,** se necessário, edite a informação vCenter.

    ![Configurar vCenter](./media/hybrid-cloud-extension-installation/configure-vcenter.png)

1. Na **Localização do Datacenter,** se necessário, edite a localização do datacenter.

    ![Localização da base de dados](./media/hybrid-cloud-extension-installation/system-location.png)

## <a name="configure-hcx"></a>Configure HCX 

1. Inscreva-se no local vCenter, e em **Casa,** selecione **HCX**.

    ![HCX em VCenter](./media/hybrid-cloud-extension-installation/hcx-vcenter.png)

1. Em **Infraestruturas**, selecione **Emparelhamento de Sítio**Adicionar um  >  **emparelhamento de site**.

    ![Adicionar emparelhamento de site](./media/hybrid-cloud-extension-installation/site-pairing.png)

1. Introduza o endereço de URL ou IP remoto HCX, nome de **Connect**utilizador e palavra-passe de GS

   O sistema mostra o local ligado.
   
    ![Ligação do site](./media/hybrid-cloud-extension-installation/site-connection.png)

1. Em **Infraestruturas**, **selecione Interconnect**  >  **Multi-Site Service Mesh**Network  >  **Profiles**Create Network  >  **Profile**.

    ![Criar perfil de rede](./media/hybrid-cloud-extension-installation/create-network-profile.png)

1. Para o novo perfil de rede, insira as gamas de endereços HCX IX e NE IP (é necessário um mínimo de dois endereços IP para aparelhos IX e NE).
    
   ![Insira intervalos de endereços IP](./media/hybrid-cloud-extension-installation/enter-address-ranges.png)
  
   > [!NOTE]
   > O aparelho de extensão de rede (HCX-NE) tem uma relação de um para um com um interruptor virtual distribuído (DVS).  

1. Selecione **perfil de computação**  >  **Crie o perfil de computação**.

1. Introduza um nome de perfil computacional e **selecione Continue**.  

    ![Criar perfil computacional](./media/hybrid-cloud-extension-installation/create-compute-profile.png)

1. Selecione os serviços para ativar, tais como migração, extensão de rede ou recuperação de desastres, e, em seguida, **selecione Continue**.

    ![Selecione serviços](./media/hybrid-cloud-extension-installation/select-services.png)

1. Em **Select Service Resources**, selecione um ou mais recursos de serviço para os quais os serviços HCX selecionados devem ser ativados. Selecione **Continuar**.
    
   ![Selecione recursos de serviço](./media/hybrid-cloud-extension-installation/select-service-resources.png)
  
   > [!NOTE]
   > Selecione clusters específicos em que os VMs de origem são direcionados para a migração usando HCX.

1. Selecione **Datastore** e **selecione Continue**. 
      
    Selecione cada recurso de cálculo e armazenamento para a implantação dos aparelhos de interligação HCX. Quando vários recursos são selecionados, o HCX utiliza o primeiro recurso selecionado até que a sua capacidade seja esgotada.  
    
    ![Selecione recursos de implantação](./media/hybrid-cloud-extension-installation/deployment-resources.png)

1. Selecione o perfil de rede de gestão criado nos **Perfis de Rede** e selecione **Continue**.  
      
    Selecione o perfil de rede através do qual a interface de gestão do vCenter e dos anfitriões ESXi podem ser alcançados. Se ainda não definiu tal perfil de rede, pode criá-lo aqui.  
    
    ![Selecione perfil de rede de gestão](./media/hybrid-cloud-extension-installation/management-network-profile.png)

1. Selecione **Network Uplink** e selecione **Continue**.
      
    Selecione um ou mais perfis de rede para que um dos seguintes seja verdadeiro:  
    * Os Aparelhos interligados no local remoto podem ser alcançados através desta rede  
    * Os aparelhos de lado remoto podem chegar aos aparelhos interligados locais através desta rede.  
    
    Se tiver redes ponto a ponto como o Direct Connect que não são partilhadas em vários sites, pode saltar este passo, uma vez que os perfis de computação são partilhados com vários sites. Nesses casos, os perfis da Rede Uplink podem ser ultrapassados e especificados durante a criação da malha de serviço de interligação.  
    
    ![Selecione o perfil de Rede Uplink](./media/hybrid-cloud-extension-installation/uplink-network-profile.png)

1. Selecione **vMotion Network Profile** e selecione **Continue**.
      
   Selecione o perfil de rede através do qual a interface vMotion dos anfitriões ESXi pode ser alcançada. Se ainda não definiu tal perfil de rede, pode criá-lo aqui. Se não tiver rede vMotion, selecione **Perfil de Rede de Gestão**.  
    
   ![Selecione o perfil vMotion Network](./media/hybrid-cloud-extension-installation/vmotion-network-profile.png)

1. A partir do **perfil de rede de replicação vSphere,** selecione um perfil de rede a interface de replicação vSphere dos anfitriões ESXi e, em seguida, selecione **Continue**.
      
   Na maioria dos casos, este perfil é o mesmo que o Perfil de Rede de Gestão.  
    
   ![Selecione o perfil da rede de replicação vSphere](./media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png)

1. A partir de **Switches Distribuídos Selecionados para Extensões de Rede**, selecione os DVS nos quais tem redes os VMs que serão integrados e estão ligados.  Selecione **Continuar**.  
      
    ![Selecione comutadores virtuais distribuídos](./media/hybrid-cloud-extension-installation/distributed-switches.png)

1. Reveja as regras de ligação e **selecione Continuar**.  

    ![Criar perfil computacional](./media/hybrid-cloud-extension-installation/complete-compute-profile.png)

1.  Selecione **Acabamento** para criar o perfil de cálculo.

## <a name="configure-network-uplink"></a>Configurar a rede Uplink

Agora configurar a mudança de perfil de rede no AVS SDDC para Uplink de rede.

1. Inscreva-se no SDDC NSX-T para criar um novo interruptor lógico, ou utilize um interruptor lógico existente que pode ser usado para uplink de rede entre as instalações e o AVS SDDC.

1. Crie um perfil de rede para uplink HCX em AVS SDDC que pode ser usado para comunicações AVS SDDC.  
    
   ![Criar perfil de rede para uplink](./media/hybrid-cloud-extension-installation/network-profile-uplink.png)

1. Introduza um nome para o perfil de rede e pelo menos 4-5 endereços IP gratuitos com base na extensão de rede L2 necessária.  
    
   ![Configurar o perfil de rede para uplink](./media/hybrid-cloud-extension-installation/configure-uplink-profile.png)

1. Selecione **Criar** para completar a configuração DO SDDC AVS

## <a name="configure-service-mesh"></a>Rede de serviço de configuração

Agora configurar a malha de serviço entre as instalações e a AVS SDDC.

1. Inscreva-se no AVS SDDC vCenter e selecione **HCX**.

2. Em **Infraestruturas**, selecione **Interconnect**  >  **Service Mesh**Create Service  >  **Mesh** para configurar a rede e computar perfis criados em etapas anteriores.    
      
    ![Rede de serviço de configuração](./media/hybrid-cloud-extension-installation/configure-service-mesh.png)

3. Selecione sites emparelhados para ativar a capacidade híbrida e **selecione Continue**.   
    
    ![Selecione sites emparelhados](./media/hybrid-cloud-extension-installation/select-paired-sites.png)

4. Selecione os perfis de cálculo de origem e remota para permitir serviços de hibrididade e selecione **Continue**.
      
    As seleções definem os recursos, onde os VMs podem consumir serviços HCX.  
      
    ![Permitir serviços de hibridez](./media/hybrid-cloud-extension-installation/enable-hybridity.png)

5. Selecione serviços para ativar e selecionar **Continue**.  
      
    ![Selecione serviços HCX](./media/hybrid-cloud-extension-installation/hcx-services.png)

6. Em **Configuração Avançada - Os perfis de rede de uplink de override** selecionam **Continue**.  
      
    Os perfis de rede de ligação são utilizados para ligar à rede através da qual os aparelhos de interligação do site remoto podem ser alcançados.  
      
    ![Superar perfis de uplink](./media/hybrid-cloud-extension-installation/override-uplink-profiles.png)

7. Selecione **Configurar a escala do aparelho de extensão de rede para fora**. 
      
    ![Escala de extensão de rede para fora](./media/hybrid-cloud-extension-installation/network-extension-scale-out.png)

8. Introduza a contagem do aparelho correspondente à contagem do interruptor DVS.  
      
    ![Contagem de aparelhos de configuração](./media/hybrid-cloud-extension-installation/appliance-scale.png)

9. **Selecione Continue** a saltar.  
      
    ![Configurar a engenharia de tráfego](./media/hybrid-cloud-extension-installation/traffic-engineering.png)

10. Reveja a pré-visualização da topologia e **selecione Continue**. 

11. Introduza um nome fácil de utilizar para esta malha de serviço e **selecione Terminar** para completar.  
      
    ![Malha de serviço completa](./media/hybrid-cloud-extension-installation/complete-service-mesh.png)

    A malha de serviço está implantada e configurada.  
      
    ![Malha de serviço implantada](./media/hybrid-cloud-extension-installation/deployed-service-mesh.png)

## <a name="check-appliance-status"></a>Verifique o estado do aparelho
Para verificar o estado do **Interconnect**aparelho, selecione  >  **Interligação .** 
      
![Estado do aparelho](./media/hybrid-cloud-extension-installation/appliance-status.png)

## <a name="next-steps"></a>Próximos passos

Quando o aparelho interligar o **estado do túnel** é **UP** e verde, está pronto para migrar e proteger os VMs AVS utilizando HCX. Consulte [a documentação VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) e [máquinas virtuais migratórias com VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) na documentação técnica VMware.
