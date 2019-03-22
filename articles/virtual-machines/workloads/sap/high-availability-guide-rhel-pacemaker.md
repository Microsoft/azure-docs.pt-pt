---
title: Configurar Pacemaker no Red Hat Enterprise Linux no Azure | Documentos da Microsoft
description: Como configurar Pacemaker no Red Hat Enterprise Linux no Azure
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
ms.openlocfilehash: 1a8e5fd82b44577aa1915d59fc7c29900a1f14ea
ms.sourcegitcommit: 5e4ca656baf3c7d370ab3c0fbad0278aa2c9f1e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58319521"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>Como configurar Pacemaker no Red Hat Enterprise Linux no Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#memory-preserving-maintenance

> [!NOTE]
> Pacemaker no Red Hat Enterprise Linux utiliza o agente de cerca de Azure para fence um nó de cluster, se necessário. Uma ativação pós-falha pode demorar até 15 minutos, se falha de parar um recurso ou os nós do cluster não é possível comunicar que uns aos outros mais. Para obter mais informações, leia [VM do Azure em execução como um membro do cluster de elevada disponibilidade do RHEL demorar muito tempo a ser bloqueados ou falha a delimitação por barreiras / horizontalmente vezes antes da VM encerra o tempo limite](https://access.redhat.com/solutions/3408711)

Leia primeiro o SAP Notes e os documentos seguintes:

* A nota SAP [1928533], que tem:
  * A lista de tamanhos de VM do Azure que são suportados para a implementação de software da SAP.
  * Informações de capacidade importante para os tamanhos de VM do Azure.
  * O software suportado do SAP e sistema operativo (SO) e combinações de base de dados.
  * A versão de kernel SAP necessária para Windows e Linux no Microsoft Azure.
* A nota SAP [2015553] apresenta uma lista de pré-requisitos para implementações de software SAP suportadas para SAP no Azure.
* A nota SAP [2002167] recomendado configurações do sistema operacional para Red Hat Enterprise Linux
* A nota SAP [2009879] tem diretrizes do SAP HANA para Red Hat Enterprise Linux
* A nota SAP [2178632] tem informações detalhadas sobre todas as monitorizações métricas comunicadas para o SAP no Azure.
* A nota SAP [2191498] tem a versão necessária do agente de anfitrião do SAP para o Linux no Azure.
* A nota SAP [2243692] tem informações sobre o licenciamento de SAP no Linux no Azure.
* A nota SAP [1999351] tem informações adicionais de resolução de problemas avançada de monitorização a extensão do Azure para SAP.
* [WIKI de Comunidade do SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as necessárias SAP Notes para o Linux.
* [Máquinas de virtuais de planeamento e implementação para o SAP no Linux do Azure][planning-guide]
* [Implementação de máquinas virtuais do Azure para SAP no Linux (Este artigo)][deployment-guide]
* [Implementação de DBMS de máquinas virtuais do Azure para SAP no Linux][dbms-guide]
* [Replicação de sistema de SAP HANA num cluster de pacemaker](https://access.redhat.com/articles/3004101)
* Documentação geral RHEL
  * [Descrição geral do suplemento de elevada disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administração de complemento de elevada disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referência de complemento de elevada disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Documentação do RHEL específica do Azure:
  * [Políticas de suporte para Clusters de elevada disponibilidade do RHEL - máquinas de virtuais do Microsoft Azure como membros do Cluster](https://access.redhat.com/articles/3131341)
  * [Instalar e configurar um Cluster de elevada disponibilidade Linux 7.4 (e posterior) de Enterprise do Red Hat no Microsoft Azure](https://access.redhat.com/articles/3252491)

## <a name="cluster-installation"></a>Instalação de cluster

![Pacemaker no Descrição geral do RHEL](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

Os seguintes itens são prefixados com ambos **[A]** - aplicáveis a todos os nós, **[1]** – apenas aplicável no nó 1 ou **[2]** – apenas aplicável a nó 2.

1. **[A]**  Registar

   Registe as máquinas virtuais e anexá-lo a um conjunto que contém os repositórios para RHEL 7.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Tenha em atenção que ao anexar um conjunto a uma imagem RHEL de PAYG do Azure Marketplace, será cobrado efetivamente double para a sua utilização do RHEL: uma vez para a imagem PAYG e, uma vez para a elegibilidade do RHEL no conjunto é anexar. Para atenuar isso, o Azure agora fornece BYOS RHEL imagens. Estão disponíveis mais informações [aqui](https://aka.ms/rhel-byos).

1. **[A]**  Ativar RHEL para repositórios SAP

   Para instalar os pacotes necessários, ative os seguintes repositórios.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable="rhel-sap-for-rhel-7-server-rpms"
   </code></pre>

1. **[A]**  Instalar o suplemento do HA RHEL

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

1. **[A]**  Configurar a resolução de nomes de anfitrião

   Pode utilizar um servidor DNS ou modificar os /etc/hosts em todos os nós. Este exemplo mostra como utilizar o ficheiro /etc/hosts.
   Substitua o endereço IP e o nome de anfitrião nos seguintes comandos. A vantagem de utilizar /etc/hosts é que o seu cluster se torna independentes do DNS que também poderia ser um ponto único de falhas.

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

1. **[A]**  Adicionar regras de firewall para pacemaker

   Adicione as seguintes regras de firewall para todas as comunicações de cluster entre os nós de cluster.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]**  Ativar os serviços de cluster básico

   Execute os seguintes comandos para ativar o serviço de Pacemaker e iniciá-la.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]**  Cluster Pacemaker criar

   Execute os seguintes comandos para autenticar os nós e criar o cluster. Defina o token para 30000 para permitir que a memória preservação da manutenção. Para obter mais informações, consulte [este artigo para Linux][virtual-machines-linux-maintenance].

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

1. **[A]**  Conjunto esperados de votos

   <pre><code>sudo pcs quorum expected-votes 2
   </code></pre>

## <a name="create-stonith-device"></a>Criar dispositivo STONITH

O dispositivo STONITH utiliza um Principal de serviço para autorizar com o Microsoft Azure. Siga estes passos para criar um Principal de serviço.

1. Ir para <https://portal.azure.com>
1. Abra o painel Azure Active Directory vá para propriedades e anote o ID de diretório. Este é o **ID de inquilino**.
1. Clique em registos de aplicações
1. Clique em Adicionar
1. Introduza um nome, selecione o tipo de aplicação "Aplicação/API da Web", introduza um URL de início de sessão (por exemplo, http:\//localhost) e clique em criar
1. O URL de início de sessão não é utilizado e pode ser qualquer URL válido
1. Selecione a nova aplicação e clique em chaves no separador Definições
1. Introduza uma descrição para uma nova chave, selecione "Nunca expira" e clique em Guardar
1. Anote o valor. Ele é usado como o **palavra-passe** para o Principal de serviço
1. Anote o ID da aplicação. Ele é usado como o nome de utilizador (**ID de início de sessão** nos passos abaixo) de Principal de serviço

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Criar uma função personalizada para o agente de cerca

O Principal de serviço não tem permissões para aceder aos seus recursos do Azure por predefinição. Tem de conceder as permissões do Principal de serviço para iniciar e parar (desaloque) todas as máquinas virtuais do cluster. Se já não tiver criado a função personalizada, pode criá-la utilizando [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) ou [da CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)

Utilize o seguinte conteúdo para o ficheiro de entrada. Precisa adaptar o conteúdo para as suas subscrições, substitua c276fc76-9cd4-44c9-99a7-4fd71546436e e e91d47c4-76f3-4271-a796-21b4ecfe3624 com os Ids da sua subscrição. Se tiver apenas uma subscrição, remova a segunda entrada assignablescopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
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

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]**  Atribuir a função personalizada para o Principal de serviço

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

Utilize o seguinte comando para configurar o dispositivo cerca.

> [!NOTE]
> Opção de 'pcmk_host_map' só é necessária no comando, se os nomes de anfitrião do RHEL e os nomes de nó do Azure não são idênticos. Consulte a seção em negrito no comando.

<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> power_timeout=240 pcmk_reboot_timeout=900</code></pre>

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]**  Permitem a utilização de um dispositivo STONITH

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

## <a name="next-steps"></a>Passos Seguintes

* [Máquinas de virtuais de planeamento e implementação de SAP do Azure][planning-guide]
* [Implementação de máquinas virtuais do Azure para SAP][deployment-guide]
* [Implementação de DBMS de máquinas virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer a elevada disponibilidade e o plano de recuperação após desastre do SAP HANA em VMs do Azure, veja [disponibilidade elevada do SAP HANA em máquinas virtuais do Azure (VMs)][sap-hana-ha]
