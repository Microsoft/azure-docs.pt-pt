---
title: Benefícios da utilização de ficheiros Azure NetApp com base de dados Oracle [ Microsoft Docs
description: Descreve a tecnologia e proporciona uma comparação de desempenho entre o Oracle Direct NFS (dNFS) e o cliente nfS tradicional. Mostra as vantagens de utilizar o DNFS com ficheiros Azure NetApp.
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
ms.openlocfilehash: 56322dc8def288ed388713e143f6b77816360ba3
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117051"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>Benefícios da utilização do Azure NetApp Files com o Oracle Database

A Oracle Direct NFS (dNFS) permite conduzir um desempenho superior ao do próprio controlador NFS do sistema operativo. Este artigo explica a tecnologia e proporciona uma comparação de desempenho entre o dNFS e o cliente tradicional nFS (Kernel NFS). Também mostra as vantagens e a facilidade de usar o dNFS com ficheiros Azure NetApp.  

## <a name="how-oracle-direct-nfs-works"></a>Como funciona o Oracle Direct NFS

O resumo seguinte explica como o Oracle Direct NFS funciona a um nível elevado:

* O Oracle Direct NFS contorna a cache tampão do sistema operativo. Os dados são cached apenas uma vez no espaço do utilizador, eliminando a sobrecarga de cópias de memória.  

* O cliente tradicional nFS usa um único fluxo de rede como mostrado abaixo:    

    ![Cliente NFS tradicional usando um único fluxo de rede](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

    O Oracle Direct NFS melhora ainda mais o desempenho através do tráfego de rede de equilíbrio de carga em vários fluxos de rede. Como testado e mostrado abaixo, 650 ligações de rede distintas foram estabelecidas dinamicamente pela Base de Dados Oracle:  

    ![Oracle Direct NFS melhorando o desempenho](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

O [Oracle FAQ para Direct NFS](http://www.orafaq.com/wiki/Direct_NFS) mostra que o Oracle dNFS é um cliente NFS otimizado. Proporciona acesso rápido e escalável ao armazenamento nFS que está localizado em dispositivos de armazenamento NAS (acessível em TCP/IP). o dNFS é incorporado no núcleo de base de dados tal como o ASM, que é utilizado principalmente com armazenamento DAS ou SAN. Como tal, *a orientação é utilizar o dNFS na implementação do armazenamento nas e utilizar as ASM na implementação do armazenamento SAN.*

dNFS é a opção padrão no Oráculo 18c.

o dNFS está disponível a partir da Oracle Database 11g. O diagrama abaixo compara o DNFS com o NFS nativo. Quando se utiliza o DNFS, uma base de dados da Oracle que funciona numa máquina virtual Azure pode conduzir mais I/O do que o cliente nativo nFS.

![Oracle e Azure NetApp Files comparação de dNFS com NFS nativo](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

Pode ativar ou desativar o DNFS executando dois comandos e reiniciando a base de dados.

Para permitir:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

Para desativar:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>Ficheiros Azure NetApp combinados com Oráculo Direct NFS

Pode melhorar o desempenho do Oracle dNFS com o serviço Azure NetApp Files. O serviço dá-lhe total controlo sobre o desempenho da sua aplicação. Pode atender aplicações extremamente exigentes. A combinação de Oracle dNFS com Ficheiros Azure NetApp proporciona uma grande vantagem às suas cargas de trabalho.

## <a name="next-steps"></a>Passos seguintes

- [Arquiteturas de solução com o Azure NetApp Files](azure-netapp-files-solution-architectures.md)
- [Visão geral das aplicações e soluções da Oracle no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-overview)
