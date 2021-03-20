---
title: Benefícios da utilização de ficheiros Azure NetApp com | de base de dados da Oracle Microsoft Docs
description: Descreve a tecnologia e proporciona uma comparação de desempenho entre a Oracle Direct NFS (dNFS) e o cliente tradicional NFS. Mostra as vantagens de usar dNFS com ficheiros Azure NetApp.
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
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: b-juche
ms.openlocfilehash: 0f431015caf5bb3364fe5628a599f760c6318c47
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91932503"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>Benefícios da utilização do Azure NetApp Files com o Oracle Database

A Oracle Direct NFS (dNFS) permite conduzir um desempenho mais elevado do que o próprio controlador NFS do sistema operativo. Este artigo explica a tecnologia e proporciona uma comparação de desempenho entre o dNFS e o cliente tradicional NFS (Kernel NFS). Também mostra as vantagens e a facilidade de usar dNFS com ficheiros Azure NetApp.  

## <a name="how-oracle-direct-nfs-works"></a>Como funciona a Oracle Direct NFS

O seguinte resumo explica como a Oracle Direct NFS funciona a um nível elevado:

* O Oracle Direct NFS contorna a cache do tampão do sistema operativo. Os dados são cached apenas uma vez no espaço do utilizador, eliminando a sobrecarga de cópias de memória.  

* O cliente tradicional NFS utiliza um único fluxo de rede, como mostrado abaixo:    

    ![Cliente tradicional da NFS usando um fluxo de rede único](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

    A Oracle Direct NFS melhora ainda mais o desempenho através do tráfego de rede de equilíbrio de carga em vários fluxos de rede. Como testado e mostrado abaixo, 650 ligações de rede distintas foram estabelecidas dinamicamente pela Base de Dados do Oráculo:  

    ![Oracle Direct NFS melhorando o desempenho](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

O [FaQ oracle para NFS direto](http://www.orafaq.com/wiki/Direct_NFS) mostra que a Oracle dNFS é um cliente NFS otimizado. Proporciona acesso rápido e escalável ao armazenamento NFS localizado em dispositivos de armazenamento NAS (acessível ao longo de TCP/IP). dNFS é incorporado no núcleo da base de dados tal como o ASM, que é usado principalmente com armazenamento DAS ou SAN. Como tal, *a orientação é utilizar o dNFS ao implementar o armazenamento nas NAS e utilizar o ASM ao implementar o armazenamento SAN.*

dNFS é a opção padrão no Oracle 18c.

dNFS está disponível a partir da Oracle Database 11g. O diagrama abaixo compara o dNFS com o NFS nativo. Quando utiliza dNFS, uma base de dados oracle que funciona numa máquina virtual Azure pode conduzir mais E/O do que o cliente nativo da NFS.

![Oracle e Azure NetApp Files comparam dNFS com NFS nativo](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

Pode ativar ou desativar o DNFS executando dois comandos e reiniciando a base de dados.

Para ativar:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

Para desativar:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>Ficheiros Azure NetApp combinados com NFS Diretos da Oracle

Pode melhorar o desempenho da Oracle dNFS com o serviço Azure NetApp Files. O serviço dá-lhe total controlo sobre o desempenho da sua aplicação. Pode atender aplicações extremamente exigentes. A combinação de Oracle dNFS com Azure NetApp Files proporciona uma grande vantagem às suas cargas de trabalho.

## <a name="next-steps"></a>Passos seguintes

- [Arquiteturas de solução com o Azure NetApp Files](azure-netapp-files-solution-architectures.md)
- [Visão geral das aplicações e soluções da Oracle no Azure](../virtual-machines/workloads/oracle/oracle-overview.md)