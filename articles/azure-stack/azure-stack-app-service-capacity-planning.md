---
title: Planeamento da capacidade para funções de servidor do App Service do Azure no Azure Stack | Documentos da Microsoft
description: Planeamento da capacidade para funções de servidor do App Service do Azure no Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/13/2019
ms.openlocfilehash: 06bafbcf3e668ba17b1245b9352e942e02569997
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57852377"
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Planeamento da capacidade para funções de servidor do App Service do Azure no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Para configurar uma implementação de produção pronto do serviço de aplicações do Azure no Azure Stack, terá de planear a capacidade de espera que o sistema para oferecer suporte.  

Este artigo fornece orientações para o número mínimo de instâncias de computação e de computação SKUs, deve usar para qualquer implementação de produção.

Pode planejar sua estratégia de capacidade do serviço de aplicações com essas diretrizes.

| Função de servidor do serviço de aplicações | Mínimo recomendado para número de instâncias | SKU de computação recomendada|
| --- | --- | --- |
| Controlador | 2 | A1 |
| Front-end | 2 | A1 |
| Gestão | 2 | A3 |
| Publicador | 2 | A1 |
| Funções de Web - partilhadas | 2 | A1 |
| Funções de Web - dedicadas | 2 por escalão | A1 |

## <a name="controller-role"></a>Função de controlador

**Mínima recomendada**: Duas instâncias do A1 Standard

O controlador de serviço de aplicações do Azure, normalmente, experiências de baixo consumo de CPU, memória e recursos de rede. No entanto, para elevada disponibilidade, tem de ter dois controladores. Dois controladores também são o número máximo de controladores permitido. Pode criar o segundo controlador de web sites direta do instalador durante a implementação.

## <a name="front-end-role"></a>Função front-end

**Mínima recomendada**: Duas instâncias do A1 Standard

Os pedidos de front-end de rotas para funções de web, dependendo da disponibilidade da função de trabalho web. Para elevada disponibilidade, deve ter mais de um front-end e pode ter mais de dois. Para fins de planejamento de capacidade, considere o que cada núcleo consegue processar cerca de 100 pedidos por segundo.

## <a name="management-role"></a>Função de gestão

**Mínima recomendada**: Duas instâncias do A3 Standard

A função de gestão do serviço de aplicações do Azure é responsável pelo App Service do Azure Resource Manager e pontos finais de API, as extensões de portais (administração, inquilino, o portal de funções) e o serviço de dados. A função de servidor de gestão normalmente requer apenas a 4 GB de RAM num ambiente de produção. No entanto, ele poderá experienciar altos níveis de CPU quando são executadas muitas tarefas de gestão (por exemplo, a criação de web site). Para elevada disponibilidade, deve ter mais de um servidor atribuído a esta função e, pelo menos dois núcleos por servidor.

## <a name="publisher-role"></a>Função de publicador

**Mínima recomendada**: Duas instâncias do A1 Standard

Se o número de utilizadores que está a publicar em simultâneo, a função de publicador poderá ter de uso intensivo da CPU. Para elevada disponibilidade, certifique-se de mais de uma função de publicador está disponível. O publicador processa apenas o tráfego FTP/FTPS.

## <a name="web-worker-role"></a>Função de trabalho Web

**Mínima recomendada**: Duas instâncias do A1 Standard

Para elevada disponibilidade, deve ter, pelo menos, quatro funções de trabalho da web, dois para o modo do site de web compartilhado e dois para cada escalão de worker dedicado que planeia disponibilizar. Partilhados e dedicados de computação modos fornecem diferentes níveis de serviço aos inquilinos. Poderá ter funções de web mais se muitos dos seus clientes são:

- A utilizar escalões de worker de modo de cálculo dedicado (que são com muitos recursos).
- Em execução no modo de cálculo partilhado.

Depois de um utilizador tiver criado um plano de serviço de aplicações para um SKU do modo de cálculo dedicado, o número de trabalho (s) web especificado nesse plano do serviço de aplicações não se encontra disponível para utilizadores.

Para fornecer as funções do Azure para os utilizadores no modelo de plano de consumo, tem de implementar funções de web compartilhado.

Ao decidir o número de funções de trabalho web partilhada a utilizar, consulte estas considerações:

- **Memória**: A memória é o recurso mais crítico para uma função de trabalho do web. Memória insuficiente afeta o desempenho do web site quando a memória virtual é trocada do disco. Cada servidor requer aproximadamente 1,2 GB de RAM para o sistema operativo. RAM acima deste limiar pode ser utilizado para executar web sites.
- **Percentagem de web sites do Active Directory**: Normalmente, cerca de 5 por cento dos aplicativos num serviço de aplicações do Azure na implementação do Azure Stack estão ativos. No entanto, a porcentagem de aplicativos que estão ativas em determinado momento pode ser superior ou inferior. Com uma taxa de aplicação Active Directory de % 5, o número máximo de aplicações para colocar num serviço de aplicações do Azure na implementação do Azure Stack deve ser inferior a 20 vezes o número de sites Active Directory (5 x 20 = 100).
- **Requisitos de espaço de memória média**: Os requisitos de espaço de memória médio para aplicações observados em ambientes de produção são cerca de 70 MB. Usando esta presença, a memória alocada em todos os computadores de função de trabalho de web ou VMs pode ser calculada da seguinte forma:

   `Number of provisioned applications * 70 MB * 5% - (number of web worker roles * 1044 MB)`

   Por exemplo, se existirem 5.000 aplicativos no ambiente que está a executar 10 funções de trabalho da web, cada função de trabalho web VM deve ter 7060 MB de RAM:

   `5,000 * 70 * 0.05 – (10 * 1044) = 7060 (= about 7 GB)`

   Para obter informações sobre como adicionar mais instâncias de função de trabalho, consulte [adicionar mais funções de trabalho](azure-stack-app-service-add-worker-roles.md).

### <a name="additional-considerations-for-dedicated-workers-during-upgrade-and-maintenance"></a>Considerações adicionais para funções de trabalho dedicadas durante a atualização e manutenção

Durante a atualização e manutenção de funções de trabalho, o serviço de aplicações do Azure no Azure Stack irá efetuar manutenção em 20% de cada escalão de worker num dado momento.  Por conseguinte, os administradores de nuvem tem sempre de manter um conjunto de 20% dos trabalhadores não alocados por escalão de worker para garantir que seus inquilinos não se deparam com qualquer perda de serviço durante a atualização e manutenção.  Por exemplo, se tiver 10 funções de trabalho num escalão de worker deve garantir que 2 são não alocado para permitir que a atualização e manutenção, se o total de 10 funções de trabalho se tornar alocadas deve aumentar o escalão de worker verticalmente para manter um conjunto de trabalhos não alocados. Durante a manutenção e atualização do App Service do Azure irá mover cargas de trabalho para trabalhadores não alocados para garantir que as cargas de trabalho irão continuar a funcionar no entanto se existirem não existem trabalhos não alocados disponíveis durante a atualizar, em seguida, lá vão ser o potencial para a carga de trabalho de inquilino tempo de inatividade.  Com respeito a funções de trabalho partilhadas, os clientes não precisa de aprovisionar trabalhos adicionais, como o serviço irá alocar aplicações de inquilino dentro de trabalhadores disponíveis automaticamente, para elevada disponibilidade, no entanto, é um requisito mínimo para os dois operadores desta escalão.

Os administradores de nuvem podem monitorizar as suas alocações de escalão de worker na área de administração do serviço de aplicações no portal de administração do Azure Stack.  Navegue para o serviço de aplicações e, em seguida, selecione os escalões de Worker no painel esquerdo.  A tabela de escalões de Worker mostra o nome do escalão de worker, tamanho, imagem utilizada, o número de workers disponíveis (não alocado), número total de trabalhos em cada camada e o estado geral do escalão de worker.

![Administração de serviço de aplicações - escalões de Worker][1]

## <a name="file-server-role"></a>Função de servidor de ficheiros

Para a função de servidor de ficheiros, pode utilizar um servidor de ficheiros autónomo para desenvolvimento e teste; Por exemplo, quando implementar o serviço de aplicações do Azure no Azure Stack Development Kit (ASDK) pode utilizar este [modelo](https://aka.ms/appsvconmasdkfstemplate).  Para fins de produção, deve utilizar um servidor de ficheiros Windows pré-configurado ou um servidor de ficheiros de não-Windows previamente configurada.

Em ambientes de produção, a função de servidor de ficheiros experiências de e/s de disco intensiva. Uma vez que ele hospeda todos os ficheiros de conteúdo e de aplicações para os sites de utilizador, deve configurar previamente um dos seguintes recursos para esta função:

- Servidor de ficheiros do Windows
- Cluster de servidores de ficheiros do Windows
- Servidor de ficheiros de não-Windows
- Cluster de servidor de ficheiros de não-Windows
- Dispositivo NAS (armazenamento de ligação de rede)

Veja o seguinte artigo para obter mais informações, [aprovisionar um servidor de ficheiros](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>Passos Seguintes

Consulte o artigo seguinte para obter mais informações:

[Antes de começar com o serviço de aplicações no Azure Stack](azure-stack-app-service-before-you-get-started.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-capacity-planning/worker-tier-allocation.png