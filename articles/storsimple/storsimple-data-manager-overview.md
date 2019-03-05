---
title: Descrição geral do Microsoft Azure StorSimple Data Manager | Documentos da Microsoft
description: Fornece uma descrição geral do serviço StorSimple Data Manager
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
ms.author: vidarmsft
ms.openlocfilehash: c5ffe3ec2ec3cb06297df6be4ba7021f692633bf
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57312496"
---
# <a name="storsimple-data-manager-solution-overview"></a>Descrição geral da solução do StorSimple Data Manager

## <a name="overview"></a>Descrição geral

O Microsoft Azure StorSimple utiliza o armazenamento na cloud como uma extensão da solução no local e automaticamente os dados de escalões armazenamento no local e na nuvem. Dados são armazenados na cloud num formato compactado e com eliminação de duplicados para uma máxima eficiência e reduzir os custos. Como os dados são armazenados no formato do StorSimple, não é prontamente consumível por outros aplicativos de nuvem que pretende utilizar.

O StorSimple Data Manager permite-lhe uma forma totalmente integrada aceder e utilizar os StorSimple formatar dados na cloud. Ele faz isso, transformando o formato do StorSimple para blobs nativos e ficheiros, o que pode utilizar com outros serviços, como serviços de multimédia do Azure, o Azure HDInsights e o Azure Machine Learning.

Este artigo fornece uma descrição geral da solução StorSimple Data Manager. Também explica como pode utilizar este serviço para escrever aplicativos que usam dados do StorSimple e outros serviços do Azure na cloud.

## <a name="how-it-works"></a>Como funciona?

O serviço do StorSimple Data Manager identifica os dados na nuvem a partir de um dispositivo de locais de série StorSimple 8000 do StorSimple. Os dados do StorSimple na cloud é duplicados, comprimidos o formato do StorSimple. O serviço de Gestor de dados fornece APIs para extrair os dados de formato do StorSimple e transformá-lo em outros formatos, como blobs do Azure e ficheiros do Azure. Isso transformou os dados, em seguida, é facilmente consumidos por serviços do Azure HDInsight e o suporte de dados do Azure. A transformação de dados, portanto, permite que esses serviços a operar os dados transformados do StorSimple a partir de dispositivos do StorSimple 8000 series no local. Este fluxo está ilustrado no diagrama seguinte.

![Diagrama de alto nível](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Casos de utilização do Gestor de dados

Pode utilizar o Gestor de dados com as funções do Azure, a automatização do Azure e o Azure Data Factory para que os fluxos de trabalho em execução nos seus dados à medida que entram no StorSimple. Talvez queira processar o suporte de dados de conteúdo que armazenar no StorSimple com serviços de multimédia do Azure, ou executar um algoritmo de Machine Learning nesses dados ou abrir um cluster do Hadoop para analisar os dados que armazena no StorSimple. A ampla gama de serviços disponíveis no Azure, juntamente com os dados no StorSimple, pode desbloquear o poder dos seus dados.


## <a name="region-availability"></a>Disponibilidade de região

O StorSimple Data Manager está disponível nas seguintes 7 regiões:

 - Sudeste Asiático
 - EUA Leste
 - EUA Oeste
 - EUA Oeste 2
 - EUA Centro-Oeste
 - Europa do Norte
 - Europa Ocidental

No entanto, o StorSimple Data Manager pode ser utilizado para transformar dados nas seguintes regiões. 

![Regiões disponíveis para os dados](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

Este conjunto é maior do que uma vez que a implementação de recursos em qualquer uma das regiões acima é capaz de levar o processo de transformação no abaixo regiões. Por isso, enquanto os dados residirem em qualquer uma das 19 regiões, pode transformar seus dados com este serviço.


## <a name="choosing-a-region"></a>Escolher uma região

Recomendamos que:
 - Sua conta de armazenamento de origem (aquele associados com o dispositivo StorSimple) e a conta de armazenamento de destino (onde pretende que os dados no formato nativo) estar na mesma região do Azure.
 - É apresentada a definição da sua Data Manager e o trabalho na região que contém a conta de armazenamento do StorSimple. Se não for possível, trazer até o Gestor de dados na região do Azure mais próxima e, em seguida, crie a definição de tarefa na mesma região que a sua conta de armazenamento StorSimple. 

    Se a sua conta de armazenamento do StorSimple não está a ser as 26 regiões que suportam a criação da definição de tarefa, recomendamos que não execute StorSimple Data Manager como pode ver latências longo e potenciais custos de saída.
    
A Microsoft está empenhada garantir que os serviços do Azure estão sempre disponíveis em todas as regiões. No entanto, as falhas de serviço não planeada podem ocorrer por curtos períodos numa determinada região. Nesses casos, pode abrir uma definição de Gestor de dados e a tarefa numa região que não é afetada pela falha e executar a tarefa de transformação. Poderá encontrar alguma latência adicional neste cenário, mas isso pode ser a sua estratégia de recuperação na rara eventualidade de uma falha regional.

## <a name="security-considerations"></a>Considerações de segurança

O StorSimple Data Manager tem da chave de encriptação de dados do serviço para transformar do formato do StorSimple para o formato nativo. A chave de encriptação de dados do serviço é gerada quando o primeiro dispositivo registra com o serviço do StorSimple. Para obter mais informações sobre esta chave, aceda a [StorSimple segurança](storsimple-8000-security.md).

A chave de encriptação de dados do serviço fornecido como entrada, é armazenado num cofre de chaves que é criado quando cria um Gestor de dados. O Cofre reside na mesma região do Azure como seu Gestor de dados do StorSimple. Esta chave é eliminada quando elimina o seu serviço de Gestor de dados.

Esta chave é utilizada por recursos de computação para executar a transformação. Estes computação recursos estão localizados na mesma região do Azure como a definição de tarefa. Esta região pode ou não ser o mesmo que a região que lhe permite utilizar o seu Gestor de dados.

Se a sua região de Gestor de dados é diferente da sua região de definição de tarefa, é importante que entenda que dados/metadados reside em cada uma dessas regiões. O diagrama seguinte ilustra os efeitos da regiões diferentes para a definição de Gestor de dados e a tarefa.

![Definição de serviço e a tarefa em diferentes regiões](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="managing-personal-information"></a>Gerenciamento de informações pessoais

O Gestor de dados do StorSimple não recolher ou apresentar quaisquer informações pessoais. Para obter mais informações, reveja a política de privacidade da Microsoft no [Centro de Fidedignidade](https://www.microsoft.com/trustcenter).

## <a name="known-limitations"></a>Limitações Conhecidas

O serviço tem, atualmente, as seguintes limitações:
- O Gestor de dados do StorSimple não funciona atualmente com volumes que são criptografados do bitlocker. Irá ver falhas de tarefas, se tentar executar o serviço com uma unidade encriptada.
- Alguns metadados de ficheiros (incluindo ACLs) não serão mantidos nos dados transformados.
- Este serviço funciona apenas com NTFS volumes.
- Comprimentos de caminhos de ficheiro tem de ser inferior a 256 carateres mais que a tarefa irá falhar.

## <a name="next-steps"></a>Passos Seguintes

[Use o StorSimple Data Manager interface de Usuário para transformar os seus dados](storsimple-data-manager-ui.md).
