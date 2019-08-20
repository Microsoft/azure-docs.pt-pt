---
title: Configurando o pacemaker em Red Hat Enterprise Linux no Azure | Microsoft Docs
description: Configurando o pacemaker no Red Hat Enterprise Linux no Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/17/2018
ms.author: sedusch
ms.openlocfilehash: 24e7e67145b789737484fcb594f48127c41eafe9
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575221"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>Configurando o pacemaker no Red Hat Enterprise Linux no Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1999351]: https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot


Leia as seguintes notas e documentos SAP primeiro:

* Nota SAP [1928533], que tem:
  * A lista de tamanhos de VM do Azure com suporte para a implantação do software SAP.
  * Informações de capacidade importantes para tamanhos de VM do Azure.
  * O software SAP com suporte e as combinações de sistema operacional e banco de dados.
  * A versão de kernel do SAP necessária para Windows e Linux em Microsoft Azure.
* O SAP Note [2015553] lista os pré-requisitos para implantações de software SAP com suporte no SAP no Azure.
* A observação do SAP [2002167] tem as configurações do sistema operacional recomendadas para Red Hat Enterprise Linux
* A observação do SAP [2009879] tem diretrizes SAP HANA para Red Hat Enterprise Linux
* A nota SAP [2178632] tem informações detalhadas sobre todas as métricas de monitoramento relatadas para SAP no Azure.
* A nota SAP [2191498] tem a versão do agente de host do SAP necessária para Linux no Azure.
* A nota SAP [2243692] tem informações sobre o licenciamento SAP no Linux no Azure.
* A nota SAP [1999351] tem informações adicionais para solução de problemas para a extensão de monitoramento avançado do Azure para SAP.
* O [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as notas SAP necessárias para o Linux.
* [Planejamento e implementação de máquinas virtuais do Azure para SAP no Linux][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP no Linux (este artigo)][deployment-guide]
* [Implantação de DBMS de máquinas virtuais do Azure para SAP no Linux][dbms-guide]
* [SAP HANA a replicação do sistema no cluster pacemaker](https://access.redhat.com/articles/3004101)
* Documentação geral do RHEL
  * [Visão geral do complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administração de complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referência de complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Documentação do RHEL específica do Azure:
  * [Políticas de suporte para clusters de alta disponibilidade RHEL-Máquinas Virtuais do Microsoft Azure como membros do cluster](https://access.redhat.com/articles/3131341)
  * [Instalando e configurando um cluster de alta disponibilidade Red Hat Enterprise Linux 7,4 (e posterior) no Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Configurar o SAP S/4HANA ASCS/ERS com o servidor de enfileiramento autônomo 2 (ENSA2) no pacemaker no RHEL 7,6](https://access.redhat.com/articles/3974941)

## <a name="cluster-installation"></a>Instalação de cluster

![Visão geral do pacemaker on RHEL](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

Os seguintes itens são prefixados com ambos **[A]** - aplicáveis a todos os nós, **[1]** – apenas aplicável no nó 1 ou **[2]** – apenas aplicável a nó 2.

1. **[A]** registrar

   Registre suas máquinas virtuais e anexe-a a um pool que contém repositórios para RHEL 7.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Observe que, ao anexar um pool a uma imagem do PAYG RHEL do Azure Marketplace, você será efetivamente cobrado por seu uso do RHEL: uma vez para a imagem PAYG e uma vez para o direito de RHEL no pool que você anexar. Para atenuar isso, o Azure agora fornece imagens BYOS RHEL. Mais informações estão disponíveis [aqui](https://aka.ms/rhel-byos).

1. **[A]** habilitar RHEL para SAP repositórios

   Para instalar os pacotes necessários, habilite os seguintes repositórios.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** instalar o complemento RHEL ha

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > Recomendamos as seguintes versões do agente de isolamento do Azure (ou posterior) para que os clientes se beneficiem de um tempo de failover mais rápido, se uma interrupção de recurso falhar ou se os nós de cluster não conseguirem se comunicar mais um com o outro:  
   > RHEL 7,6: Fence-Agents-4.2.1 -11. EL7 _ 6.8  
   > RHEL 7,5: Fence-Agents-4.0.11 -86. EL7 _ 5.8  
   > RHEL 7,4: Fence-Agents-4.0.11 -66. EL7 _ 4.12  
   > Para obter mais informações, consulte [a VM do Azure em execução como um membro de cluster de alta disponibilidade do RHEL leva muito tempo para ser decrescente ou o isolamento falha/expira antes de a VM ser](https://access.redhat.com/solutions/3408711)desligada.

   Verifique a versão do agente de limite do Azure. Se necessário, atualize-o para uma versão igual ou posterior à especificada acima.

   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

   > [!IMPORTANT]
   > Se você precisar atualizar o agente de limite do Azure e, se estiver usando a função personalizada, certifique-se de atualizar a função personalizada para incluir a ação **estado desligado**. Para obter detalhes, consulte [criar uma função personalizada para o agente de isolamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker#1-create-a-custom-role-for-the-fence-agent).  

1. **[A]**  Configurar a resolução de nomes de anfitrião

   Pode utilizar um servidor DNS ou modificar os /etc/hosts em todos os nós. Este exemplo mostra como utilizar o ficheiro /etc/hosts.
   Substitua o endereço IP e o nome de anfitrião nos seguintes comandos. A vantagem de utilizar /etc/hosts é que o seu cluster se torna independente de DNS, que também poderia ser um ponto único de falhas.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Insira as seguintes linhas ao /etc/hosts. Alterar o endereço IP e o nome de anfitrião para corresponder ao seu ambiente

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]**  Alterar hacluster palavra-passe para a mesma palavra-passe

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** adicionar regras de firewall para pacemaker

   Adicione as regras de firewall a seguir a todas as comunicações de cluster entre os nós de cluster.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** habilitar serviços de cluster básicos

   Execute os comandos a seguir para habilitar o serviço pacemaker e iniciá-lo.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** criar cluster pacemaker

   Execute os comandos a seguir para autenticar os nós e criar o cluster. Defina o token como 30000 para permitir a manutenção da preservação da memória. Para obter mais informações, consulte [Este artigo para Linux][virtual-machines-linux-maintenance].

   <pre><code>sudo pcs cluster auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup --name <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> --token 30000
   sudo pcs cluster start --all

   # Run the following command until the status of both nodes is online
   sudo pcs status

   # Cluster name: nw1-azr
   # WARNING: no stonith devices and stonith-enabled is not false
   # Stack: corosync
   # Current DC: <b>prod-cl1-1</b> (version 1.1.18-11.el7_5.3-2b07d5c5a9) - partition with quorum
   # Last updated: Fri Aug 17 09:18:24 2018
   # Last change: Fri Aug 17 09:17:46 2018 by hacluster via crmd on <b>prod-cl1-1</b>
   #
   # 2 nodes configured
   # 0 resources configured
   #
   # Online: [ <b>prod-cl1-0</b> <b>prod-cl1-1</b> ]
   #
   # No resources
   #
   #
   # Daemon Status:
   #   corosync: active/disabled
   #   pacemaker: active/disabled
   #   pcsd: active/enabled
   </code></pre>

1. **[A]** definir votos esperados

   <pre><code>sudo pcs quorum expected-votes 2
   </code></pre>

## <a name="create-stonith-device"></a>Criar dispositivo STONITH

O dispositivo STONITH utiliza um Principal de serviço para autorizar com o Microsoft Azure. Siga estes passos para criar um Principal de serviço.

1. Ir para <https://portal.azure.com>
1. Abra o painel Azure Active Directory  
   Vá para propriedades e anote o ID de diretório. Este é o **ID de inquilino**.
1. Clique em registos de aplicações
1. Clique em novo registro
1. Insira um nome, selecione "contas somente neste diretório da organização" 
2. Selecione o tipo de aplicativo "Web", insira uma URL de logon (por exemplo,\/http:/localhost) e clique em Adicionar  
   O URL de início de sessão não é utilizado e pode ser qualquer URL válido
1. Selecione certificados e segredos e clique em novo segredo do cliente
1. Insira uma descrição para uma nova chave, selecione "nunca expira" e clique em Adicionar
1. Anote o valor. Ele é usado como o **palavra-passe** para o Principal de serviço
1. Selecione visão geral. Anote o ID da aplicação. Ele é usado como o nome de utilizador (**ID de início de sessão** nos passos abaixo) de Principal de serviço

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Criar uma função personalizada para o agente de cerca

O Principal de serviço não tem permissões para aceder aos seus recursos do Azure por predefinição. Você precisa conceder permissões de entidade de serviço para iniciar e parar (desligar) todas as máquinas virtuais do cluster. Se já não tiver criado a função personalizada, pode criá-la utilizando [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) ou [da CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)

Utilize o seguinte conteúdo para o ficheiro de entrada. Precisa adaptar o conteúdo para as suas subscrições, substitua c276fc76-9cd4-44c9-99a7-4fd71546436e e e91d47c4-76f3-4271-a796-21b4ecfe3624 com os Ids da sua subscrição. Se tiver apenas uma subscrição, remova a segunda entrada assignablescopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to power-off and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/powerOff/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** atribuir a função personalizada à entidade de serviço

Atribua a função personalizada "Linux cerca agente de função" que foi criado no último capítulo para o Principal de serviço. Não utilize a função de proprietário mais!

1. Ir para https://portal.azure.com
1. Abra o painel de todos os recursos
1. Selecione a máquina virtual do primeiro nó de cluster
1. Clique em controle de acesso (IAM)
1. Clique em Adicionar atribuição de função
1. Selecione a função de "Função de agente de cerca de Linux"
1. Introduza o nome da aplicação que criou acima
1. Clicar em Guardar

Repita os passos acima para o segundo nó de cluster.

### <a name="1-create-the-stonith-devices"></a>**[1]**  Criar os dispositivos STONITH

Depois de editar as permissões para as máquinas virtuais, pode configurar os dispositivos STONITH no cluster.

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

Use o comando a seguir para configurar o dispositivo de isolamento.

> [!NOTE]
> A opção ' pcmk_host_map ' só será necessária no comando se os nomes de host do RHEL e os nomes dos nós do Azure não forem idênticos. Consulte a seção negrito no comando.

<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> power_timeout=240 pcmk_reboot_timeout=900</code></pre>

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** habilitar o uso de um dispositivo STONITH

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

## <a name="next-steps"></a>Passos seguintes

* [Planejamento e implementação de máquinas virtuais do Azure para SAP][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP][deployment-guide]
* [Implantação de DBMS de máquinas virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer alta disponibilidade e planejar a recuperação de desastre de SAP HANA em VMs do Azure, consulte [alta disponibilidade de SAP Hana em VMS (máquinas virtuais) do Azure][sap-hana-ha]
