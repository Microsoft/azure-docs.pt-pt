---
title: Melhores práticas
description: Melhores práticas recomendadas para obter melhores resultados
author: ariye
ms.author: crtreasu
ms.date: 03/12/2021
ms.topic: best-practice
ms.service: azure-object-anchors
ms.openlocfilehash: e287d8305b3fd85fc992417e1563b1e58e6f8424
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463005"
---
# <a name="best-practices"></a>Melhores práticas

Recomendamos experimentar alguns destes passos para obter os melhores resultados.

## <a name="ingestion"></a>Ingestão

- Verifique as dimensões dos seus objetos físicos. As âncoras do Objeto Azure funcionam melhor para objetos cuja menor dimensão se encontra no intervalo de 1m-10m recomendado.
- Inspecione o seu modelo 3D em software como o [**MeshLab**](https://www.meshlab.net/) para obter os seguintes detalhes.
  - Certifique-se de que o modelo 3D tem uma malha triangular e que os triângulos na superfície exterior estão virados para fora. Ou seja, os vértices devem ser orientados para que as normas sigam a regra da direita na sua orientação para fora.
  - Certifique-se de que o Modelo 3D é especificado com as unidades de escala corretas no que diz respeito aos objetos físicos. As unidades devem ser uma de: ***Centímetros, Decimetros, Pés, Centímetros, Quilómetros, Metros, Milímetros, Estaleiros.***
  - Confirme a direção de gravidade nominal que corresponde à orientação vertical do objeto no mundo real. Se a vertical/gravidade do objeto for -Y, use ***(0, -1, 0)** _ ou _*_ (0, 0, -1)_** para -Z, e igualmente para qualquer outra direção.
  - Certifique-se de que o modelo 3D está codificado num dos formatos suportados: `.glb` , , , . . `.gltf` `.ply` `.fbx` `.obj` .
- O nosso serviço de conversão de modelos pode demorar muito tempo a processar um grande modelo lod alto (nível de detalhe). Para eficácia pode pré-processar o seu modelo 3D para remover as faces interiores.

## <a name="detection"></a>Deteção

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Mixed-Reality/Azure-Object-Anchors-Detection-and-Alignment-Best-Practices/player]

- O tempo de execução fornecido pela SDK requer uma região de pesquisa fornecida pelo utilizador para procurar e detetar os objetos físicos. A região de busca pode ser uma caixa de delimitação, uma esfera, uma vista frustum, ou qualquer combinação deles. Para evitar uma deteção falsa, é preferível definir uma região de pesquisa suficientemente grande para cobrir o objeto. Ao utilizar as aplicações de amostra fornecidas, pode ficar de um lado do objeto a cerca de 2 metros da superfície mais próxima e iniciar a aplicação.
- Antes de iniciar a aplicação Azure Object Anchors num dispositivo HoloLens 2, remova os hologramas nas proximidades do seu local de trabalho através das definições principais dos seus dispositivos através ***de Hologramas >sistema de Definições >***

  Este passo garante que se um novo objeto como um carro estiver presente no mesmo espaço que ocupado por outro anteriormente, ou se o objeto se moveu do espaço alvo, quaisquer hologramas antigos e irrelevantes não persistirão e criarão uma visualização confusa para o objeto atualmente à vista.
- Depois de remover os hologramas e antes de iniciar a aplicação, digitalize o objeto como um carro olhando para o objeto enquanto usa o dispositivo a cerca de 1-2 metros e andando lentamente ao redor do objeto uma ou duas vezes.

  Este passo garante que quaisquer estimativas residuais de superfície criadas no seu espaço por objetos anteriores e digitalizações são refrescadas com as superfícies do objeto alvo atual com o quais vai trabalhar. Caso contrário, a aplicação poderá ver superfícies fantasma duplas que conduzam a um alinhamento impreciso do seu modelo 3D e dos hologramas associados. A pré-digitalização do objeto também reduzirá consideravelmente a latência de deteção de âncoras de objetos Azure, por exemplo, de 30 segundos para 5 segundos.
- Para objetos escuros e altamente reflexivos, poderá ter de digitalizar o objeto mais perto e também movendo a cabeça para cima e para baixo e para a esquerda e para a direita para que o dispositivo veja superfícies de múltiplos ângulos e múltiplas distâncias.
- Quando vir uma deteção errada de objetos, como a orientação a ser virada ou a pose incorreta, tal como um modelo inclinado, deve visualizar o mapeamento espacial. Muitas vezes, os resultados incorretos devem-se a uma reconstrução superficial deficiente ou incompleta. Pode remover os hologramas, digitalizar o objeto e executar novamente a deteção de objetos na aplicação.
- O SDK de execução fornecido fornece alguns parâmetros para permitir aos utilizadores afinar a deteção, como demonstrado nas nossas aplicações de amostra. Os parâmetros padrão funcionam bem para a maioria dos objetos. Se descobrir que precisa de os ajustar para objetos específicos, aqui ficam algumas recomendações:
  - Utilize um limiar de cobertura de superfície inferior se o objeto físico for grande, escuro ou brilhante.
  - Permita uma alteração de pequena escala (por exemplo, 0.1) para objetos grandes como um carro.
  - Permitir algum desvio em graus entre a direção vertical local do objeto e a gravidade quando o objeto estiver numa inclinação.
