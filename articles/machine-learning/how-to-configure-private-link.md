---
title: Configurar um ponto final privado (pré-visualização)
titleSuffix: Azure Machine Learning
description: Utilize o Azure Private Link para aceder de forma segura ao seu espaço de trabalho Azure Machine Learning a partir de uma rede virtual.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/21/2020
ms.openlocfilehash: 619960238125191e7bd4e702a49016c8fd58c847
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91296659"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>Configure Azure Private Link para um espaço de trabalho de aprendizagem de máquinas Azure (pré-visualização)

Neste documento, aprende a utilizar o Azure Private Link com o seu espaço de trabalho Azure Machine Learning. Para obter informações sobre a criação de uma rede virtual para Azure Machine Learning, consulte [o isolamento da rede virtual e a visão geral da privacidade](how-to-network-security-overview.md)

> [!IMPORTANT]
> A utilização do Azure Private Link com o espaço de trabalho Azure Machine Learning está atualmente em pré-visualização pública. Esta funcionalidade só está disponível nas seguintes regiões:
>
> * **E.U.A. Leste**
> * **E.U.A. Centro-Sul**
> * **E.U.A. Oeste**
> * **E.U.A. Oeste 2**
> * **Canadá Central**
> * **Sudeste Asiático**
> * **Leste do Japão**
> * **Europa do Norte**
> * **Leste da Austrália**
> * **Sul do Reino Unido**
>
> Esta pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Azure Private Link permite-lhe ligar-se ao seu espaço de trabalho utilizando um ponto final privado. O ponto final privado é um conjunto de endereços IP privados dentro da sua rede virtual. Em seguida, pode limitar o acesso ao seu espaço de trabalho para ocorrer apenas sobre os endereços IP privados. O Private Link ajuda a reduzir o risco de exfiltração de dados. Para saber mais sobre os pontos finais privados, consulte o artigo [Azure Private Link.](/azure/private-link/private-link-overview)

> [!IMPORTANT]
> O Azure Private Link não afeta o plano de controlo Azure (operações de gestão) como a eliminação do espaço de trabalho ou a gestão de recursos computacional. Por exemplo, criar, atualizar ou eliminar um alvo de computação. Estas operações são realizadas através da Internet pública normalmente. As operações de plano de dados, tais como a utilização do estúdio Azure Machine Learning, APIs (incluindo oleodutos publicados), ou o SDK usam o ponto final privado.
>
> Poderá encontrar problemas ao tentar aceder ao ponto final privado para o seu espaço de trabalho se estiver a utilizar o Mozilla Firefox. Este problema pode estar relacionado com DNS sobre HTTPS na Mozilla. Recomendamos a utilização do Microsoft Edge do Google Chrome como uma solução alternativa.

> [!TIP]
> A exemplo de computação Azure Machine Learning pode ser usada com um espaço de trabalho e ponto final privado. Esta capacidade está atualmente em pré-visualização pública nas regiões **norte-americanas do Leste,** **Central Sul dos EUA** e EUA West **2.**

## <a name="prerequisites"></a>Pré-requisitos

Se planeia utilizar um link privado habilitado espaço de trabalho com uma chave gerida pelo cliente, deve solicitar esta funcionalidade usando um bilhete de apoio. Para obter mais informações, consulte [Gerir e aumentar as quotas.](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Criar um espaço de trabalho que use um ponto final privado

> [!IMPORTANT]
> Atualmente, só apoiamos a viabilização de um ponto final privado na criação de um novo espaço de trabalho para aprendizagem automática Azure.

O modelo [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) pode ser usado para criar um espaço de trabalho com um ponto final privado.

Para obter informações sobre a utilização deste modelo, incluindo pontos finais privados, consulte [utilize um modelo de Gestor de Recursos Azure para criar um espaço de trabalho para a aprendizagem automática Azure](how-to-create-workspace-template.md).

## <a name="using-a-workspace-over-a-private-endpoint"></a>Usando um espaço de trabalho sobre um ponto final privado

Uma vez que a comunicação ao espaço de trabalho só é permitida a partir da rede virtual, quaisquer ambientes de desenvolvimento que utilizem o espaço de trabalho devem ser membros da rede virtual. Por exemplo, uma máquina virtual na rede virtual.

> [!IMPORTANT]
> Para evitar perturbações temporárias da conectividade, a Microsoft recomenda a descarga da cache DNS nas máquinas que se ligam ao espaço de trabalho depois de permitir o Private Link. 

Para obter informações sobre máquinas virtuais Azure, consulte a [documentação das Máquinas Virtuais.](/azure/virtual-machines/)


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a segurança do seu espaço de trabalho Azure Machine Learning, consulte o artigo de visão geral do [isolamento e privacidade da rede Virtual.](how-to-network-security-overview.md)
