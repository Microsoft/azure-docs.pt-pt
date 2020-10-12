---
title: Visão geral do Microsoft Azure StorSimple Data Manager Microsoft Docs
description: Conheça a solução StorSimple Data Manager e como pode utilizar este serviço para escrever aplicações que utilizam dados StorSimple e outros serviços Azure.
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
ms.openlocfilehash: d683f49cadb384ef59d3bae819156733691813cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88183501"
---
# <a name="storsimple-data-manager-solution-overview"></a>Visão geral do StorSimple Data Manager

## <a name="overview"></a>Descrição geral

O Microsoft Azure StorSimple utiliza o armazenamento em nuvem como uma extensão da solução no local e tiers automaticamente dados através do armazenamento no local e na nuvem. Os dados são armazenados na nuvem num formato deduped e comprimido para a máxima eficiência e para reduzir custos. Como os dados são armazenados em formato StorSimple, não é facilmente consumível por outras aplicações em nuvem que possa querer utilizar.

O StorSimple Data Manager permite-lhe aceder e utilizar os dados do formato StorSimple na nuvem. Fá-lo transformando o formato StorSimple em blobs e ficheiros nativos, que pode utilizar com outros serviços como a Azure Media Services, Azure HDInsights e Azure Machine Learning.

Este artigo fornece uma visão geral da solução StorSimple Data Manager. Também explica como pode usar este serviço para escrever aplicações que utilizam dados StorSimple e outros serviços Azure na nuvem.

## <a name="how-it-works"></a>Como funciona?

O serviço StorSimple Data Manager identifica dados StorSimple na nuvem a partir de um dispositivo storSimple 8000 série no local. Os dados StorSimple na nuvem são deduped, comprimido storSimple formato. O serviço Data Manager fornece APIs para extrair os dados do formato StorSimple e transformá-lo em outros formatos, tais como blobs Azure e Ficheiros Azure. Estes dados transformados são então facilmente consumidos pelos serviços Azure HDInsight e Azure Media. A transformação de dados permite assim que estes serviços operem com base nos dados transformados da StorSimple do dispositivo storSimple 8000 no local. Este fluxo é ilustrado no diagrama seguinte.

![Diagrama de alto nível](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Casos de utilização do Gestor de Dados

Pode utilizar o Gestor de Dados com Funções Azure, Azure Automation e Azure Data Factory para ter fluxos de trabalho a funcionar nos seus dados à medida que entra em StorSimple. Pode querer processar o seu conteúdo de mídia que armazena no StorSimple com a Azure Media Services, ou executar um algoritmo de Machine Learning nesses dados, ou criar um cluster Hadoop para analisar os dados que armazena no StorSimple. Com a vasta gama de serviços disponíveis no Azure combinados com os dados do StorSimple, pode desbloquear a energia dos seus dados.


## <a name="region-availability"></a>Disponibilidade de região

O Gestor de Dados StorSimple está disponível nas seguintes 7 regiões:

 - Sudeste Asiático
 - E.U.A. Leste
 - E.U.A. Oeste
 - E.U.A. Oeste 2
 - E.U.A. Centro-Oeste
 - Europa do Norte
 - Europa Ocidental

No entanto, o StorSimple Data Manager pode ser usado para transformar dados nas seguintes regiões. 

![Regiões disponíveis para dados](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

Este conjunto é maior porque a implantação de recursos em qualquer uma das regiões acima é capaz de elevar o processo de transformação nas regiões abaixo. Assim, desde que os seus dados residam em qualquer uma das 19 regiões, pode transformar os seus dados usando este serviço.


## <a name="choosing-a-region"></a>Escolher uma região

É recomendável que:
 - A sua conta de armazenamento de origem (a associada ao seu dispositivo StorSimple) e a conta de armazenamento alvo (onde pretende os dados em formato nativo) estão na mesma região de Azure.
 - Você traz o seu Gestor de Dados e definição de emprego na região que contém a conta de armazenamento StorSimple. Se isso não for possível, traga o Gestor de Dados na região de Azure mais próxima e, em seguida, crie a Definição de Emprego na mesma região que a sua conta de armazenamento StorSimple. 

    Se a sua conta de armazenamento StorSimple não estiver nas 26 regiões que suportam a criação de definição de emprego, recomendamos que não execute o StorSimple Data Manager, uma vez que vê longas latências e potenciais encargos de saída.
    
A Microsoft esforça-se por garantir que os serviços Azure estão sempre disponíveis em todas as regiões. No entanto, podem ocorrer interrupções de serviço não planeadas durante curtos períodos de tempo numa determinada região. Nesses casos, pode apresentar um Gestor de Dados e uma definição de emprego numa região que não é afetada pela paralisação, e executar o trabalho de transformação. Você pode encontrar alguma latência adicional em tal cenário, mas esta pode ser a sua estratégia de recuperação em caso raro de uma paragem regional.

## <a name="security-considerations"></a>Considerações de segurança

O Gestor de Dados StorSimple precisa da chave de encriptação de dados de serviço para se transformar do formato StorSimple para formato nativo. A chave de encriptação de dados de serviço é gerada quando o primeiro dispositivo se regista com o serviço StorSimple. Para obter mais informações sobre esta chave, aceda à [segurança StorSimple](storsimple-8000-security.md).

A chave de encriptação de dados de serviço fornecida como uma entrada é armazenada num cofre chave que é criado quando cria um Data Manager. O cofre reside na mesma região de Azure que o seu Gestor de Dados StorSimple. Esta chave é eliminada quando apaga o seu serviço Dedundo de Dados.

Esta chave é usada pelos recursos computacionais para realizar a transformação. Estes recursos computacional estão localizados na mesma região de Azure que a sua definição de trabalho. Esta região pode, ou não, ser a mesma que a região onde você cria o seu Gestor de Dados.

Se a região do Data Manager é diferente da sua região de definição de emprego, é importante que compreenda quais os dados/metadados que residem em cada uma destas regiões. O diagrama que se segue ilustra o efeito de ter diferentes regiões para gestor de dados e definição de emprego.

![Definição de serviço e emprego em diferentes regiões](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="managing-personal-information"></a>Gestão de informações pessoais

O Gestor de Dados StorSimple não recolhe nem exibe qualquer informação pessoal. Para obter mais informações, reveja a política de privacidade da Microsoft no [Centro de Fidedignidade](https://www.microsoft.com/trustcenter).

## <a name="known-limitations"></a>Limitações Conhecidas

O serviço tem atualmente as seguintes limitações:
- Atualmente, o StorSimple Data Manager não funciona com volumes encriptados bitlocker. Verá falhas de emprego se tentar executar o serviço com uma unidade encriptada.
- Alguns metadados de ficheiros (incluindo ACLs) não serão retidos nos dados transformados.
- Este serviço funciona apenas com volumes NTFS.
- Os comprimentos dos caminhos de arquivo têm de ser inferiores a 256 caracteres, o trabalho falhará.

## <a name="next-steps"></a>Passos seguintes

[Utilize o StorSimple Data Manager UI para transformar os seus dados](storsimple-data-manager-ui.md).
