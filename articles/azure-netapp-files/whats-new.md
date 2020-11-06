---
title: Novidades nos Ficheiros Azure NetApp Microsoft Docs
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
ms.date: 11/05/2020
ms.author: b-juche
ms.openlocfilehash: 3f13d997f73e9737b8770eec7e0742d50bf1abb8
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421707"
---
# <a name="whats-new-in-azure-netapp-files"></a>Novidades nos ficheiros Azure NetApp

Os Ficheiros Azure NetApp são atualizados regularmente. Este artigo fornece um resumo sobre as novidades e melhorias mais recentes. 

## <a name="november-2020"></a>novembro de 2020

* [Instantâneo reverter](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert)

    A funcionalidade de reverte instantânea permite-lhe reverter rapidamente um volume para o estado em que estava quando uma determinada imagem foi tirada. Na maioria dos casos, reverter um volume é muito mais rápido do que restaurar ficheiros individuais de uma imagem para o sistema de ficheiros ativos. É também mais eficiente em termos de espaço em comparação com restaurar um instantâneo para um novo volume.

## <a name="september-2020"></a>Setembro de 2020

* [Azure NetApp Files replicação inter-região](cross-region-replication-introduction.md) (Visualização pública)

  O Azure NetApp Files suporta agora a replicação transversal. Com esta nova capacidade de recuperação de desastres, pode replicar os volumes dos seus Ficheiros Azure NetApp de uma região Azure para outra de forma rápida e económica, protegendo os seus dados de falhas regionais imprevisíveis. A azure NetApp Files cruza a replicação da região alavanca a tecnologia ® NetApp SnapMirror; apenas os blocos alterados são enviados sobre a rede num formato comprimido e eficiente. Esta tecnologia proprietária minimiza a quantidade de dados necessários para se replicar em todas as regiões, poupando assim os custos de transferência de dados. Também encurta o tempo de replicação, para que possa alcançar um objetivo de ponto de restauro menor (RPO).

* [Piscina manual da capacidade QoS](manual-qos-capacity-pool-introduction.md) (pré-visualização)  

    Num pool manual de capacidade QoS, pode atribuir a capacidade e a produção para um volume de forma independente. A produção total de todos os volumes criados com um pool manual de capacidade QoS é limitada pela produção total da piscina. É determinado pela combinação do tamanho da piscina e da produção de nível de serviço. Em alternativa, o tipo [QoS](azure-netapp-files-understand-storage-hierarchy.md#qos_types) de uma piscina de capacidade pode ser automático (automático), que é o padrão. Num pool de capacidade de QoS automático, a produção é atribuída automaticamente aos volumes da piscina, proporcional à quota de tamanho atribuída aos volumes.

* [Assinatura LDAP](azure-netapp-files-create-volumes-smb.md) (Pré-visualização)   

    O Azure NetApp Files suporta agora a assinatura LDAP para pesquisas LDAP seguras entre o serviço Azure NetApp Files e os controladores de domínio de serviços de domínio de diretório ativo especificados pelo utilizador. Esta funcionalidade encontra-se em pré-visualização.

* [Encriptação AES para autenticação AD](azure-netapp-files-create-volumes-smb.md) (Pré-visualização)

    O Azure NetApp Files suporta agora a encriptação AES na ligação LDAP a DC para permitir a encriptação AES para um volume SMB. Esta funcionalidade encontra-se em pré-visualização. 

* Novas [métricas:](azure-netapp-files-metrics.md)   

    * Novas métricas de volume: 
        * *Tamanho atribuído volume* : Tamanho previsto de um volume
    * Novas métricas da piscina: 
        * *Tamanho atribuído da piscina* : O tamanho a provisionado da piscina 
        * *Tamanho total do instantâneo para a piscina* : A soma do tamanho do instantâneo de todos os volumes na piscina

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

* [Utilizadores de política de backup](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection) (Pré-visualização)

    O Azure NetApp Files permite-lhe incluir contas adicionais que requerem privilégios elevados à conta de computador criada para utilização com ficheiros Azure NetApp. As contas especificadas serão autorizadas a alterar as permissões NTFS ao nível do ficheiro ou da pasta. Por exemplo, pode especificar uma conta de serviço não privilegiada utilizada para a migração de dados para uma partilha de ficheiros SMB em Ficheiros Azure NetApp. A funcionalidade de utilizadores de política de backup está atualmente em pré-visualização.

## <a name="next-steps"></a>Passos seguintes
* [O que é o Azure NetApp Files](azure-netapp-files-introduction.md)
* [Compreender a hierarquia de armazenamento do Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md) 
