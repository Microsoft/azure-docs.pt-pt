---
title: Visão geral do Microsoft Azure StorSimple Data Manager [ Microsoft Docs
description: Fornece uma visão geral do serviço StorSimple Data Manager
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/21/2018
ms.author: alkohli
ms.openlocfilehash: 2ffe17bf7ef4f01c18d2c26f4a045add7302272d
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67876108"
---
# <a name="storsimple-data-manager-solution-overview"></a>Visão geral da solução storSimple Data Manager

## <a name="overview"></a>Descrição geral

O Microsoft Azure StorSimple utiliza o armazenamento em nuvem como uma extensão da solução no local e automaticamente niveve os dados através do armazenamento no local e na nuvem. Os dados são armazenados na nuvem num formato desenganado e comprimido para a máxima eficiência e para reduzir os custos. Como os dados são armazenados em formato StorSimple, não é facilmente consumível por outras aplicações na nuvem que você pode querer usar.

O StorSimple Data Manager permite-lhe aceder e utilizar os dados do formato StorSimple na nuvem. Fá-lo transformando o formato StorSimple em bolhas e ficheiros nativos, que pode utilizar com outros serviços, como o Azure Media Services, Azure HDInsights e Azure Machine Learning.

Este artigo fornece uma visão geral da solução StorSimple Data Manager. Também explica como pode usar este serviço para escrever aplicações que utilizam dados StorSimple e outros serviços Azure na nuvem.

## <a name="how-it-works"></a>Como funciona?

O serviço StorSimple Data Manager identifica dados StorSimple na nuvem a partir de um dispositivo de série StorSimple 8000 no local. Os dados StorSimple na nuvem são desenganados, compressado formato StorSimple. O serviço Data Manager fornece APIs para extrair os dados do formato StorSimple e transformá-lo em outros formatos, como blobs Azure e Ficheiros Azure. Estes dados transformados são então facilmente consumidos pelos serviços Azure HDInsight e Azure Media. A transformação de dados permite assim que estes serviços operem com os dados storSimple transformados do dispositivo storSimple 8000 no local. Este fluxo é ilustrado no seguinte diagrama.

![Diagrama de alto nível](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Data Manager utiliza casos

Pode utilizar o Gestor de Dados com Funções Azure, Automação Azure e Azure Data Factory para ter fluxos de trabalho em execução nos seus dados à medida que estes entram no StorSimple. Você pode querer processar o seu conteúdo de mídia que você armazena no StorSimple com Azure Media Services, ou executar um algoritmo de Machine Learning sobre esses dados, ou trazer um cluster Hadoop para analisar os dados que você armazena no StorSimple. Com a vasta gama de serviços disponíveis no Azure combinados com os dados do StorSimple, pode desbloquear a potência dos seus dados.


## <a name="region-availability"></a>Disponibilidade de região

O StorSimple Data Manager está disponível nas seguintes 7 regiões:

 - Ásia Sudeste
 - E.U.A. Leste
 - E.U.A. Oeste
 - E.U.A.Oeste 2
 - E.U.A. Centro-Oeste
 - Europa do Norte
 - Europa ocidental

No entanto, o StorSimple Data Manager pode ser usado para transformar dados nas seguintes regiões. 

![Regiões disponíveis para dados](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

Este conjunto é maior porque a implantação de recursos em qualquer uma das regiões acima referidas é capaz de trazer o processo de transformação nas regiões abaixo. Assim, desde que os seus dados residam em qualquer uma das 19 regiões, pode transformar os seus dados usando este serviço.


## <a name="choosing-a-region"></a>Escolher uma região

É recomendável que:
 - A sua conta de armazenamento de origem (a associada ao seu dispositivo StorSimple) e a conta de armazenamento alvo (onde deseja os dados em formato nativo) estão na mesma região do Azure.
 - Você traz o seu Gestor de Dados e definição de emprego na região que contém a conta de armazenamento StorSimple. Se tal não for possível, traga o Gestor de Dados na região de Azure mais próxima e, em seguida, crie a Definição de Emprego na mesma região que a sua conta de armazenamento StorSimple. 

    Se a sua conta de armazenamento StorSimple não estiver nas 26 regiões que apoiam a criação de definição de emprego, recomendamos que não execute o StorSimple Data Manager, uma vez que vê longas atrasos e potenciais encargos de saída.
    
A Microsoft esforça-se por garantir que os serviços Do Azure estão sempre disponíveis em todas as regiões. No entanto, podem ocorrer interrupções de serviço não planeadas durante curtos períodos numa determinada região. Nesses casos, pode apresentar um Gestor de Dados e definição de emprego numa região que não é afetada pela paralisação, e executar o trabalho de transformação. Você pode encontrar alguma latência adicional em tal cenário, mas esta pode ser a sua estratégia de recuperação no raro caso de uma paragem regional.

## <a name="security-considerations"></a>Considerações de segurança

O StorSimple Data Manager necessita da chave de encriptação de dados do serviço para transformar do formato StorSimple para o formato nativo. A chave de encriptação de dados de serviço é gerada quando o primeiro dispositivo se regista com o serviço StorSimple. Para mais informações sobre esta chave, vá à [segurança StorSimple](storsimple-8000-security.md).

A chave de encriptação de dados de serviço fornecida como entrada é armazenada num cofre chave que é criado quando cria um Data Manager. O cofre reside na mesma região azure que o seu StorSimple Data Manager. Esta chave é eliminada quando elimina o serviço Data Manager.

Esta chave é usada pelos recursos computacionais para realizar a transformação. Estes recursos computacionais estão localizados na mesma região de Azure que a sua definição de trabalho. Esta região pode, ou não, ser a mesma que a região onde você traz o seu Gestor de Dados.

Se a sua região de Gestor de Dados é diferente da sua região de definição de emprego, é importante que compreenda quais os dados/metadados que residem em cada uma destas regiões. O diagrama seguinte ilustra o efeito de ter diferentes regiões para Data Manager e definição de emprego.

![Definição de serviço e emprego em diferentes regiões](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="managing-personal-information"></a>Gestão de informações pessoais

O StorSimple Data Manager não recolhe nem exibe qualquer informação pessoal. Para obter mais informações, reveja a política de privacidade da Microsoft no [Centro de Fidedignidade](https://www.microsoft.com/trustcenter).

## <a name="known-limitations"></a>Limitações Conhecidas

O serviço tem atualmente as seguintes limitações:
- O StorSimple Data Manager atualmente não funciona com volumes que são encriptados. Verá falhas de emprego se tentar executar o serviço com uma unidade encriptada.
- Alguns metadados de ficheiros (incluindo ACLs) não serão retidos nos dados transformados.
- Este serviço funciona apenas com volumes NTFS.
- Os comprimentos dos caminhos dos ficheiros têm de ser inferiores a 256 caracteres, caso contrário o trabalho falhará.

## <a name="next-steps"></a>Passos seguintes

[Utilize o StorSimple Data Manager UI para transformar os seus dados](storsimple-data-manager-ui.md).
