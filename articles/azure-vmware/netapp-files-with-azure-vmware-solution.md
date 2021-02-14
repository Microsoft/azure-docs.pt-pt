---
title: Ficheiros Azure NetApp com Solução VMware Azure
description: Utilize ficheiros Azure NetApp com VMs de Solução VMware Azure para migrar e sincronizar dados em servidores no local, VMs de Solução VMware Azure e infraestruturas em nuvem.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: db7d8eb05e5bd70f6a2397b3017924093218e78e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371571"
---
# <a name="azure-netapp-files-with-azure-vmware-solution"></a>Ficheiros Azure NetApp com Solução VMware Azure

Neste artigo, vamos percorrer os passos de integração dos Ficheiros Azure NetApp com cargas de trabalho baseadas em Azure VMware Solution. O sistema operativo de hóspedes funcionará dentro de máquinas virtuais (VMs) que acedam aos volumes dos Ficheiros Azure NetApp. 

## <a name="azure-netapp-files-overview"></a>Visão geral dos ficheiros Azure NetApp

[O Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md) é um serviço Azure para migração e executa as cargas de trabalho de ficheiros empresariais mais exigentes na nuvem: bases de dados, SAP e aplicações de computação de alto desempenho, sem alterações de código.

### <a name="features"></a>Funcionalidades
(Serviços onde os ficheiros Azure NetApp são utilizados.)

- **Conexões de Diretório Ativo**: Azure NetApp Files suporta [serviços de domínio de diretório ativo e serviços de domínio de diretório ativo Azure](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#decide-which-domain-services-to-use).

- **Protocolo de partilha**: Os ficheiros Azure NetApp suportam protocolos do Bloco de Mensagens do Servidor (SMB) e do Sistema de Ficheiros de Rede (NFS). Este suporte significa que os volumes podem ser montados no cliente Linux e podem ser mapeados no cliente Windows.

- **Solução Azure VMware**: As ações do Azure NetApp Files podem ser montadas a partir de VMs que são criados no ambiente Azure VMware Solution.

O Azure NetApp Files está disponível em muitas regiões do Azure e suporta a replicação transversal. Para obter informações sobre os métodos de configuração dos Ficheiros Azure NetApp, consulte [a hierarquia de armazenamento dos ficheiros Azure NetApp](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md).

## <a name="reference-architecture"></a>Arquitetura de referência

O diagrama seguinte ilustra uma ligação via Azure ExpressRoute a uma nuvem privada Azure VMware Solution. O ambiente Azure VMware Solution acede à partilha Azure NetApp Files montada em VMs Azure VMware Solution.

![Diagrama mostrando ficheiros NetApp para arquitetura Azure VMware Solution.](media/net-app-files/net-app-files-topology.png)

Este artigo cobre instruções para configurar, testar e verificar o volume dos Ficheiros Azure NetApp como uma partilha de ficheiros para VMs de Solução VMware Azure. Neste cenário, usamos o protocolo da NFS. Os Ficheiros Azure NetApp e a Azure VMware Solution são criados na mesma região do Azure.

## <a name="prerequisites"></a>Pré-requisitos 

> [!div class="checklist"]
> * Subscrição do Azure com ficheiros Azure NetApp ativados
> * Sub-rede para ficheiros Azure NetApp
> * Linux VM na Solução VMware Azure
> * VMs do Windows na Solução VMware Azure

## <a name="regions-supported"></a>Regiões apoiadas

A lista de regiões apoiadas pode ser encontrada na [Azure Products by Region.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp,azure-vmware&regions=all)

## <a name="verify-pre-configured-azure-netapp-files"></a>Verificar ficheiros Azure NetApp pré-configurados 

Siga as instruções passo a passo nos seguintes artigos para criar e monte Azure NetApp Files volumes em VMs Azure VMware Solution.

- [Criar uma conta NetApp](../azure-netapp-files/azure-netapp-files-create-netapp-account.md)
- [Configurar um conjunto de capacidade](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
- [Criar um volume SMB para o Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)
- [Criar um volume NFS para o Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes.md)
- [Delegar uma sub-rede ao Azure NetApp Files](../azure-netapp-files/azure-netapp-files-delegate-subnet.md)

Os seguintes passos incluem a verificação dos ficheiros Azure NetApp pré-configurados criados em Azure no nível de serviço Azure NetApp Files Premium.

1. No portal Azure, em **ARMAZENAMENTO,** selecione **Ficheiros Azure NetApp**. Será apresentada uma lista dos seus ficheiros Azure NetApp configurados. 

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Screenshot mostrando lista de ficheiros Azure NetApp pré-configurados."::: 

2. Selecione uma conta NetApp Files configurada para ver as suas definições. Por exemplo, **selecione Contoso-anf2**. 

3. Selecione **piscinas de capacidade** para verificar a piscina configurada. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Screenshot mostrando opções para visualizar piscinas de capacidade e volumes de uma conta NetApp Files configurada.":::

    A página de pools capacitado abre mostrando a capacidade e o nível de serviço. Neste exemplo, a piscina de armazenamento é configurada como 4 TiB com um nível de serviço Premium.

4. Selecione **Volumes** para visualizar volumes criados sob o pool de capacidade. (Ver imagens anteriores.)

5. Selecione um volume para visualizar a sua configuração.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Screenshot mostrando volumes criados sob a capacidade do pool.":::

    Abre-se uma janela mostrando os detalhes de configuração do volume.

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="Screenshot mostrando detalhes de configuração de um volume.":::

    Você pode ver que o anfvolume tem um tamanho de 200 GiB e está na capacidade de piscina anfpool1. É exportado como uma partilha de ficheiros NFS através de 10.22.3.4:/ANFVOLUME. Um IP privado da Rede Virtual Azure (VNet) foi criado para os Ficheiros Azure NetApp e o caminho NFS para montar no VM.

    Para saber mais sobre o desempenho do volume dos Ficheiros Azure NetApp por tamanho ou "Quota", consulte [considerações de desempenho para ficheiros Azure NetApp](../azure-netapp-files/azure-netapp-files-performance-considerations.md). 

## <a name="verify-pre-configured-azure-vmware-solution-vm-share-mapping"></a>Verifique o mapeamento de ações VMware VMware de Solução VMware pré-configurado

Para tornar os seus Ficheiros Azure NetApp acessíveis ao seu VM de Solução VMware Azure, terá de compreender o mapeamento de partilha SMB e NFS. Só depois de configurar os volumes SMB ou NFS, pode montá-los como documentado aqui.

- Partilha SMB: Criar uma ligação Ative Directory antes de implantar um volume SMB. Os controladores de domínio especificados devem estar acessíveis pela sub-rede delegada dos Ficheiros Azure NetApp para uma ligação bem sucedida. Uma vez configurado o Ative Directory na conta Azure NetApp Files, aparecerá como um item selecionável enquanto cria volumes SMB.

- NFS partilha: Azure NetApp Files contribui para a criação dos volumes utilizando NFS ou protocolo duplo (NFS e SMB). O consumo de capacidade de um volume é contabilizado para a capacidade aprovisionada do seu conjunto. O NFS pode ser montado no servidor Linux utilizando as linhas de comando ou /etc/fstab entradas.

## <a name="use-cases-of-azure-netapp-files-with-azure-vmware-solution"></a>Utilizar casos de ficheiros Azure NetApp com Solução VMware Azure

Seguem-se apenas alguns casos de utilização de ficheiros Azure NetApp. 
- Gestão de perfis horizonte
- Gestão de perfis Citrix
- Gestão de perfis de serviços de desktop remoto
- Ações de ficheiros na Azure VMware Solution

## <a name="next-steps"></a>Passos seguintes

Agora que cobriu a integração de Ficheiros Azure NetApp com as suas cargas de trabalho da Azure VMware Solution, talvez queira saber mais sobre:

- [Limites de recursos para ficheiros Azure NetApp](../azure-netapp-files/azure-netapp-files-resource-limits.md#resource-limits).
- [Diretrizes para o planeamento da rede Azure NetApp Files](../azure-netapp-files/azure-netapp-files-network-topologies.md).
- [Replicação transversal dos volumes de ficheiros Azure NetApp](../azure-netapp-files/cross-region-replication-introduction.md). 
- [Perguntas frequentes sobre ficheiros Azure NetApp](../azure-netapp-files/azure-netapp-files-faqs.md).
