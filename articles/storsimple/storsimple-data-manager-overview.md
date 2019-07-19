---
title: Visão geral do Microsoft Azure StorSimple Gerenciador de Dados | Microsoft Docs
description: Fornece uma visão geral do serviço de Gerenciador de Dados do StorSimple
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
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876108"
---
# <a name="storsimple-data-manager-solution-overview"></a>Visão geral da solução de Gerenciador de Dados do StorSimple

## <a name="overview"></a>Descrição geral

O Microsoft Azure StorSimple usa o armazenamento em nuvem como uma extensão da solução local e automaticamente armazena em camadas os dados no armazenamento local e na nuvem. Os dados são armazenados na nuvem em um formato com eliminação de duplicação e compactação para obter máxima eficiência e reduzir os custos. Como os dados são armazenados no formato StorSimple, eles não são prontamente consumíveis por outros aplicativos em nuvem que você talvez queira usar.

O Gerenciador de Dados do StorSimple permite que você acesse e use diretamente os dados de formato do StorSimple na nuvem. Ele faz isso transformando o formato do StorSimple em arquivos e blobs nativos, que podem ser usados com outros serviços, como os serviços de mídia do Azure, o Azure HDInsights e o Azure Machine Learning.

Este artigo fornece uma visão geral da solução de Gerenciador de Dados do StorSimple. Ele também explica como você pode usar esse serviço para escrever aplicativos que usam dados do StorSimple e outros serviços do Azure na nuvem.

## <a name="how-it-works"></a>Como funciona?

O serviço de Gerenciador de Dados do StorSimple identifica dados do StorSimple na nuvem de um dispositivo local da série StorSimple 8000. Os dados do StorSimple na nuvem são reduplicados, formato StorSimple compactado. O serviço de Gerenciador de Dados fornece APIs para extrair os dados de formato do StorSimple e transformá-los em outros formatos, como BLOBs do Azure e arquivos do Azure. Esses dados transformados são prontamente consumidos pelo Azure HDInsight e pelos serviços de mídia do Azure. Assim, a transformação de dados permite que esses serviços operem nos dados do StorSimple transformados do dispositivo local da série StorSimple 8000. Esse fluxo é ilustrado no diagrama a seguir.

![Diagrama de alto nível](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Gerenciador de Dados casos de uso

Você pode usar o Gerenciador de Dados com Azure Functions, a automação do Azure e o Azure Data Factory para que os fluxos de trabalho sejam executados em seus dados conforme eles entram no StorSimple. Talvez você queira processar o conteúdo de mídia armazenado no StorSimple com os serviços de mídia do Azure ou executar um algoritmo de Machine Learning nesses dados ou abrir um cluster Hadoop para analisar os dados armazenados no StorSimple. Com a vasta gama de serviços disponíveis no Azure combinada com os dados no StorSimple, você pode desbloquear a potência dos seus dados.


## <a name="region-availability"></a>Disponibilidade de região

O Gerenciador de Dados do StorSimple está disponível nas seguintes 7 regiões:

 - Sudeste Asiático
 - East US
 - EUA Oeste
 - EUA Oeste 2
 - EUA Centro-Oeste
 - Europa do Norte
 - Europa Ocidental

No entanto, o Gerenciador de Dados do StorSimple pode ser usado para transformar dados nas regiões a seguir. 

![Regiões disponíveis para dados](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

Esse conjunto é maior porque a implantação de recursos em qualquer uma das regiões acima é capaz de colocar o processo de transformação nas regiões abaixo. Portanto, contanto que seus dados residam em qualquer uma das 19 regiões, você pode transformar seus dados usando esse serviço.


## <a name="choosing-a-region"></a>Escolhendo uma região

Recomendamos que:
 - Sua conta de armazenamento de origem (aquela associada ao seu dispositivo StorSimple) e a conta de armazenamento de destino (onde você deseja que os dados no formato nativo) estejam na mesma região do Azure.
 - Você coloca seu Gerenciador de Dados e a definição de trabalho na região que contém a conta de armazenamento do StorSimple. Se isso não for possível, ative o Gerenciador de Dados na região do Azure mais próxima e, em seguida, crie a definição de trabalho na mesma região que sua conta de armazenamento do StorSimple. 

    Se sua conta de armazenamento do StorSimple não estiver nas 26 regiões que dão suporte à criação de definição de trabalho, recomendamos que você não execute Gerenciador de Dados do StorSimple, pois você verá latências longas e possíveis encargos de saída.
    
A Microsoft se esforça para garantir que os serviços do Azure estejam sempre disponíveis em todas as regiões. No entanto, as interrupções de serviço não planejadas podem ocorrer por períodos curtos em uma determinada região. Nesses casos, você pode abrir uma definição de Gerenciador de Dados e de trabalho em uma região que não é afetada pela interrupção e executar o trabalho de transformação. Você pode encontrar alguma latência adicional nesse cenário, mas essa pode ser a sua estratégia de recuperação no caso raro de uma interrupção regional.

## <a name="security-considerations"></a>Considerações de segurança

O Gerenciador de Dados do StorSimple precisa da chave de criptografia de dados de serviço para transformar do formato StorSimple em formato nativo. A chave de criptografia de dados de serviço é gerada quando o primeiro dispositivo é registrado com o serviço StorSimple. Para obter mais informações sobre essa chave, vá para [segurança do StorSimple](storsimple-8000-security.md).

A chave de criptografia de dados de serviço fornecida como uma entrada é armazenada em um cofre de chaves que é criado quando você cria um Gerenciador de Dados. O cofre reside na mesma região do Azure que seu Gerenciador de Dados do StorSimple. Essa chave é excluída quando você exclui seu serviço de Gerenciador de Dados.

Essa chave é usada pelos recursos de computação para executar a transformação. Esses recursos de computação estão localizados na mesma região do Azure que a definição de trabalho. Essa região pode ou não ser a mesma que a região onde você coloca seu Gerenciador de Dados.

Se sua região de Gerenciador de Dados for diferente da sua região de definição de trabalho, é importante entender quais dados/metadados residem em cada uma dessas regiões. O diagrama a seguir ilustra o efeito de ter regiões diferentes para Gerenciador de Dados e definição de trabalho.

![Definição de serviço e trabalho em diferentes regiões](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="managing-personal-information"></a>Gerenciando informações pessoais

O Gerenciador de Dados do StorSimple não coleta nem exibe informações pessoais. Para obter mais informações, reveja a política de privacidade da Microsoft no [Centro de Fidedignidade](https://www.microsoft.com/trustcenter).

## <a name="known-limitations"></a>Limitações Conhecidas

No momento, o serviço tem as seguintes limitações:
- O Gerenciador de Dados do StorSimple atualmente não funciona com volumes que são criptografados pelo BitLocker. Você verá falhas de trabalho se tentar executar o serviço com uma unidade criptografada.
- Alguns metadados de arquivos (incluindo ACLs) não serão mantidos nos dados transformados.
- Esse serviço funciona apenas com volumes NTFS.
- Os comprimentos do caminho do arquivo devem ter menos de 256 caracteres. caso contrário, o trabalho falhará.

## <a name="next-steps"></a>Passos Seguintes

[Use Gerenciador de dados do StorSimple interface do usuário para transformar seus dados](storsimple-data-manager-ui.md).
