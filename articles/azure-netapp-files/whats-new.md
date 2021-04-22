---
title: Novidades em Azure NetApp Files | Microsoft Docs
description: Fornece um resumo sobre as mais recentes novidades e melhorias dos Ficheiros Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/21/2021
ms.author: b-juche
ms.openlocfilehash: fa028d8fffd2a4097b5bf7d7326d355ae56aebd7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862818"
---
# <a name="whats-new-in-azure-netapp-files"></a>Novidades nos ficheiros Azure NetApp

Os ficheiros Azure NetApp são atualizados regularmente. Este artigo fornece um resumo sobre as novidades e melhorias mais recentes. 

## <a name="april-2021"></a>abril de 2021

* [SMB Disponibilidade Contínua (CA) partilha suporte para recipientes de perfil de utilizador FSLogix](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) (Preview)  

    [FSLogix](/fslogix/overview) é um conjunto de soluções que melhoram, permitem e simplificam ambientes de computação windows não persistentes. As soluções FSLogix são adequadas para ambientes virtuais em nuvens públicas e privadas. As soluções FSLogix também podem ser usadas para criar sessões de computação mais portáteis quando utiliza dispositivos físicos. O FSLogix pode ser utilizado para fornecer acesso dinâmico a recipientes de perfil de utilizador persistentes armazenados em armazenamento em rede partilhado SMB, incluindo ficheiros Azure NetApp. Para aumentar ainda mais a resiliência da FSLogix aos eventos de manutenção do serviço de armazenamento, o Azure NetApp Files alargou o suporte para a SMB Transparent Failover através [de ações SMB Continuous Availability (CA) em Ficheiros Azure NetApp](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) para contentores de perfil de utilizador. Consulte as [soluções de ambiente de trabalho virtual do Windows](azure-netapp-files-solution-architectures.md#windows-virtual-desktop) Files do Azure Para obter informações adicionais.  

* [Encriptação do Protocolo SMB3](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) (Pré-visualização) 

    Agora pode ativar a encriptação do protocolo SMB3 nos ficheiros SMB Emb dos ficheiros Azure NetApp e volumes de protocolo duplo. Esta funcionalidade permite a encriptação para dados SMB3 em voo, utilizando o [algoritmo AES-CCM em SMB 3.0 e o algoritmo AES-GCM nas ligações SMB 3.1.1.](/windows-server/storage/file-server/file-server-smb-overview#features-added-in-smb-311-with-windows-server-2016-and-windows-10-version-1607) Os clientes SMB que não usam encriptação SMB3 não poderão aceder a este volume. Os dados em repouso são encriptados independentemente desta definição. A encriptação SMB aumenta ainda mais a segurança. No entanto, pode afetar o cliente (sobrecarga de CPU para encriptar e desencriptar mensagens). Pode também ter impacto na utilização dos recursos de armazenamento (reduções na produção). Deve testar o impacto do desempenho da encriptação contra as suas aplicações antes de colocar cargas de trabalho na produção.

* [Serviços de domínio de diretório ativo (ADDS) Mapeamento do utilizador LDAP com grupos alargados NFS](configure-ldap-extended-groups.md) (Pré-visualização)   

    Por padrão, o Azure NetApp Files suporta até 16 IDs de grupo ao manusear credenciais de utilizador NFS, tal como definido no [RFC 5531](https://tools.ietf.org/html/rfc5531). Com esta nova capacidade, pode agora aumentar o máximo para 1.024 se tiver utilizadores que sejam membros de mais do que o número padrão de grupos. Para suportar esta capacidade, os volumes NFS podem agora ser adicionados também ao ADD LDAP, que permite aos utilizadores do Diretório Ativo LDAP com entradas de grupos alargados (com até 1024 grupos) acederem ao volume. 

## <a name="march-2021"></a>março de 2021
 
* [Ações de Disponibilidade Contínua SMB (CA)](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) (Pré-visualização)  

    O SMB Transparent Failover permite operações de manutenção no serviço Azure NetApp Files sem interromper a conectividade com as aplicações do servidor que armazenam e acedem a dados em volumes SMB. Para suportar o SMB Transparent Failover, o Azure NetApp Files suporta agora a opção de partilha de disponibilidade contínua SMB para utilização com aplicações SQL Server sobre SMB em execução em VMs Azure. Esta funcionalidade é suportada atualmente no Windows SQL Server. O Linux SQL Server não está atualmente suportado. Ativar esta funcionalidade proporciona melhorias significativas no desempenho do SQL Server e benefícios de escala e custos para instância [única, Always-On falha de caso de cluster e Always-On implementações do Grupo de Disponibilidade](azure-netapp-files-solution-architectures.md#sql-server). Consulte [os benefícios da utilização de ficheiros Azure NetApp para a implementação do SERVIDOR SQL](solutions-benefits-azure-netapp-files-sql-server.md).

* [Redimensionamento automático de um volume de destino de replicação transversal](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)

    Numa relação de replicação entre regiões, um volume de destino é automaticamente redimensionado com base no tamanho do volume de origem. Como tal, não precisa de redimensionar o volume de destino separadamente. Este comportamento de redimensionamento automático é aplicável quando os volumes estão numa relação de replicação ativa, ou quando o espreitamento de replicação é quebrado com a operação de resync. Para que esta funcionalidade funcione, é necessário garantir espaço suficiente nas piscinas de capacidade tanto para a fonte como para os volumes de destino.

## <a name="december-2020"></a>Dezembro de 2020

* [Ferramenta instantânea consistente da aplicação Azure](azacsnap-introduction.md) (pré-visualização)    

    A azure Application Consistent Snapshot Tool (AzAcSnap) é uma ferramenta de linha de comando que permite simplificar a proteção de dados para bases de dados de terceiros (SAP HANA) em ambientes Linux (por exemplo, SUSE e RHEL).   

    O AzAcSnap aproveita as funcionalidades de instantâneo e replicação de volume em Ficheiros Azure NetApp e Azure Large Instance. Proporciona os seguintes benefícios:

    * Proteção de dados consistente com aplicação 
    * Gestão de catálogos de bases de dados 
    * *Proteção do* volume ad hoc 
    * Clonagem de volumes de armazenamento 
    * Apoio à recuperação de desastres 

## <a name="november-2020"></a>Novembro de 2020

* [Instantâneo reverter](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert)

    A funcionalidade de reverte instantânea permite-lhe reverter rapidamente um volume para o estado em que estava quando uma determinada imagem foi tirada. Na maioria dos casos, reverter um volume é muito mais rápido do que restaurar ficheiros individuais de uma imagem para o sistema de ficheiros ativos. É também mais eficiente em termos de espaço em comparação com restaurar um instantâneo para um novo volume.

## <a name="september-2020"></a>Setembro de 2020

* [Replicação transversal de ficheiros Azure NetApp](cross-region-replication-introduction.md) (Preview)

  O Azure NetApp Files suporta agora a replicação transversal. Com esta nova capacidade de recuperação de desastres, pode replicar os volumes dos seus Ficheiros Azure NetApp de uma região Azure para outra de forma rápida e económica, protegendo os seus dados de falhas regionais imprevisíveis. A azure NetApp Files cruza a replicação da região alavanca a tecnologia ® NetApp SnapMirror; apenas os blocos alterados são enviados sobre a rede num formato comprimido e eficiente. Esta tecnologia proprietária minimiza a quantidade de dados necessários para se replicar em todas as regiões, poupando assim os custos de transferência de dados. Também encurta o tempo de replicação, para que possa alcançar um objetivo de ponto de restauro menor (RPO).

* [Piscina manual da capacidade QoS](manual-qos-capacity-pool-introduction.md) (pré-visualização)  

    Num pool manual de capacidade QoS, pode atribuir a capacidade e a produção para um volume de forma independente. A produção total de todos os volumes criados com um pool manual de capacidade QoS é limitada pela produção total da piscina. É determinado pela combinação do tamanho da piscina e da produção de nível de serviço. Em alternativa, o tipo [QoS](azure-netapp-files-understand-storage-hierarchy.md#qos_types) de uma piscina de capacidade pode ser automático (automático), que é o padrão. Num pool de capacidade de QoS automático, a produção é atribuída automaticamente aos volumes da piscina, proporcional à quota de tamanho atribuída aos volumes.

* [Assinatura LDAP](azure-netapp-files-create-volumes-smb.md) (Pré-visualização)   

    O Azure NetApp Files suporta agora a assinatura LDAP para pesquisas LDAP seguras entre o serviço Azure NetApp Files e os controladores de domínio de serviços de domínio de diretório ativo especificados pelo utilizador. Esta funcionalidade encontra-se em pré-visualização.

* [Encriptação AES para autenticação AD](azure-netapp-files-create-volumes-smb.md) (Pré-visualização)

    O Azure NetApp Files suporta agora a encriptação AES na ligação LDAP a DC para permitir a encriptação AES para um volume SMB. Esta funcionalidade encontra-se em pré-visualização. 

* Novas [métricas:](azure-netapp-files-metrics.md)   

    * Novas métricas de volume: 
        * *Tamanho atribuído volume*: Tamanho previsto de um volume
    * Novas métricas da piscina: 
        * *Tamanho atribuído da piscina*: O tamanho a provisionado da piscina 
        * *Tamanho total do instantâneo para a piscina*: A soma do tamanho do instantâneo de todos os volumes na piscina

## <a name="july-2020"></a>Julho de 2020

* [Volume de protocolo duplo (NFSv3 e SMB)](create-volumes-dual-protocol.md)

    Pode agora criar um volume de Ficheiros Azure NetApp que permite o acesso simultâneo a dois protocolos (NFS v3 e SMB) com suporte para mapeamento de utilizador LDAP. Esta funcionalidade permite a utilização de casos em que possa ter uma carga de trabalho baseada no Linux que gere e armazena dados num volume de Ficheiros Azure NetApp. Ao mesmo tempo, a sua equipa precisa de utilizar clientes e software baseados no Windows para analisar os dados recentemente gerados a partir do mesmo volume de Ficheiros Azure NetApp. A funcionalidade de acesso simultânea de dois protocolos elimina a necessidade de copiar os dados gerados pela carga de trabalho para um volume separado com um protocolo diferente para pós-análise, poupança de custos de armazenamento e tempo de funcionamento. Esta funcionalidade é gratuita (o custo normal de armazenamento dos [Ficheiros Azure NetApp](https://azure.microsoft.com/pricing/details/netapp/) ainda se aplica) e está geralmente disponível. Saiba mais a partir da [documentação simultânea de acesso a dois protocolos.](create-volumes-dual-protocol.MD)

* [Encriptação NFS v4.1 Kerberos em trânsito](configure-kerberos-encryption.MD)

    O Azure NetApp Files suporta agora a encriptação do cliente NFS nos modos Kerberos (krb5, krb5i e krb5p) com encriptação AES-256, proporcionando-lhe segurança adicional de dados. Esta funcionalidade é gratuita (o custo normal de armazenamento dos [Ficheiros Azure NetApp](https://azure.microsoft.com/pricing/details/netapp/) ainda se aplica) e está geralmente disponível. Saiba mais com a documentação de [encriptação NFS v4.1 Kerberos](configure-kerberos-encryption.MD).

* [Alteração dinâmica do nível de serviço de volume](dynamic-change-volume-service-level.MD)

    Cloud promete flexibilidade nos gastos de TI. Pode agora alterar o nível de serviço de um volume de Ficheiros Azure NetApp existente, movendo o volume para outro pool de capacidade que utiliza o nível de serviço que deseja para o volume. Esta alteração ao nível de serviço no local para o volume não requer que se emigre os dados. Também não afeta o acesso do plano de dados ao volume. Pode alterar um volume existente para utilizar um nível de serviço mais elevado para um melhor desempenho, ou para utilizar um nível de serviço mais baixo para a otimização de custos. Esta funcionalidade é gratuita (o custo normal de armazenamento dos [Ficheiros Azure NetApp](https://azure.microsoft.com/pricing/details/netapp/) ainda se aplica) e encontra-se atualmente em pré-visualização pública. Pode registar-se para a pré-visualização da funcionalidade seguindo a documentação dinâmica de [alteração do nível de serviço](dynamic-change-volume-service-level.md)de volume .

* [Política de instantâneo de volume](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) (pré-visualização) 

    O Azure NetApp Files permite-lhe criar instantâneos pontuais dos seus volumes. Pode agora criar uma política instantânea para que os Ficheiros Azure NetApp criem automaticamente instantâneos de volume numa frequência à sua escolha. Pode agendar as fotos a serem tiradas em ciclos de hora, dia, semanais ou mensais. Também pode especificar o número máximo de instantâneos a manter como parte da política de instantâneos. Esta funcionalidade é gratuita (o custo normal de armazenamento dos [Ficheiros Azure NetApp](https://azure.microsoft.com/pricing/details/netapp/) ainda se aplica) e encontra-se atualmente em pré-visualização. Pode registar-se para a pré-visualização da funcionalidade seguindo a [documentação da política de instantâneos](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)de volume .

* [Política de exportação de acesso à raiz da NFS](azure-netapp-files-configure-export-policy.md)

    O Azure NetApp Files permite-lhe agora especificar se a conta raiz pode aceder ao volume. 

* [Esconder caminho instantâneo](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)

    O Azure NetApp Files permite-lhe agora especificar se um utilizador pode ver e aceder ao `.snapshot` diretório (clientes NFS) ou `~snapshot` pasta (clientes SMB) num volume montado.

## <a name="may-2020"></a>Maio de 2020

* [Utilizadores de política de backup](create-active-directory-connections.md) (Pré-visualização)

    O Azure NetApp Files permite-lhe incluir contas adicionais que requerem privilégios elevados à conta de computador criada para utilização com ficheiros Azure NetApp. As contas especificadas serão autorizadas a alterar as permissões NTFS ao nível do ficheiro ou da pasta. Por exemplo, pode especificar uma conta de serviço não privilegiada utilizada para a migração de dados para uma partilha de ficheiros SMB em Ficheiros Azure NetApp. A funcionalidade de utilizadores de política de backup está atualmente em pré-visualização.

## <a name="next-steps"></a>Passos seguintes
* [O que é o Azure NetApp Files](azure-netapp-files-introduction.md)
* [Compreender a hierarquia de armazenamento do Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md) 
