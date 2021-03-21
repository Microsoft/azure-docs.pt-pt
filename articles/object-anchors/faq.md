---
title: Perguntas mais frequentes
description: Perguntas frequentes sobre o serviço Azure Object Anchors.
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 04/01/2020
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: 6de027f317072512bd9893303d3a79d0f34f3e3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101749085"
---
# <a name="frequently-asked-questions-about-azure-object-anchors"></a>Perguntas frequentes sobre âncoras de objetos Azure

A Azure Object Anchors permite que uma aplicação detete um objeto no mundo físico usando um modelo 3D e estima a sua pose de 6-DoF.

Para obter mais informações, consulte [a visão geral das âncoras de objetos Azure](overview.md).

## <a name="product-faq"></a>FAQ do produto
**P: Que recomendações tem para os objetos que devem ser utilizados?**

**A:** Recomendamos as seguintes propriedades para objetos:

* 1-10 metros para cada dimensão
* Não simétrico, com variações suficientes na geometria
* Baixa refletividade (superfícies mate) com cor brilhante
* Objetos estacionários
* Nenhuma ou pequena quantidade de articulação
* Fundos claros sem ou mínima desordem
* Objeto digitalizado deve ter 1:1 corresponder com o modelo com que treinou

**P: Quais são as dimensões máximas do objeto que podem ser processadas para a ingestão de modelos?**

**A:** Cada dimensão de um modelo CAD deve ser inferior a 10 metros.

**P: Qual é o tamanho máximo do modelo CAD que pode ser processado para ingestão?**

**A:** O tamanho do ficheiro do modelo deve ser inferior a 150 MB.

**P: Quais são os formatos CAD suportados?**

**A:** Atualmente apoiamos `fbx` , e tipos de `ply` `obj` `glb` `gltf` ficheiros.

**P: Qual é a direção de gravidade e unidade exigida pelo serviço de ingestão de modelos? Como podemos descobri-los?**

**A:** A direção da gravidade é o vetor para baixo apontando para a terra. Para os modelos CAD, a direção da gravidade é tipicamente o oposto de uma direção para cima. Por exemplo, em muitos casos +Z representa para cima, nesse caso -Z ou `Vector3(0.0, 0.0, -1.0)` representaria a direção da gravidade. Ao determinar a gravidade, deve não só considerar o modelo, mas também a orientação em que o modelo será visto durante o tempo de funcionamento. Se você está tentando detetar uma cadeira no mundo real em uma superfície plana, a gravidade pode ser `Vector3(0.0, 0.0, -1.0)` . No entanto, se a cadeira estiver numa inclinação de 45 graus, a gravidade pode ser `Vector3(0.0, -Sqrt(2)/2, -Sqrt(2)/2)` .

A direção de gravidade pode ser fundamentada com uma ferramenta de renderização 3D, como [o MeshLab.](http://www.meshlab.net/)

A unidade representa a unidade de medição do modelo. As unidades suportadas podem ser encontradas utilizando a enumeração **Microsoft.Azure.ObjectAnchors.Ingestion.Unit.**

**P: Quanto tempo demora a ingerir um modelo CAD?**

**A:** Para um `ply` modelo, normalmente 3-15 minutos. Se submeter modelos em outros formatos, espere 15-60 minutos dependendo do tamanho do ficheiro.

**P: Que dispositivos suportam as Âncoras de Objeto?**

**A:** HoloLens 2 

**Q: Que constroem os os meus HoloLens devem funcionar?**

**A:** OS Build 18363.720 ou mais recente, lançado após 12 de março de 2020.

  Mais detalhes na [atualização do Windows 10 de março de 2020](https://support.microsoft.com/help/4551762).

**P: Quanto tempo demora a detetar um objeto em HoloLens?**

**A:** Depende do tamanho do objeto e do processo de digitalização. Para obter uma deteção mais rápida, tente seguir as melhores práticas para uma verificação completa. Para objetos menores dentro de 2 metros em cada dimensão, a deteção pode ocorrer dentro de alguns segundos. Para objetos maiores, como um carro, o utilizador deve andar em volta do objeto para obter uma deteção fiável, o que significa que a deteção pode demorar dezenas de segundos.

**P: Quais são as melhores práticas ao usar âncoras de objeto numa aplicação HoloLens?**

**A:**

 1. Execute a calibração ocular para obter uma renderização precisa.
 2. Certifique-se de que o quarto tem uma textura visual rica e uma boa iluminação.
 3. Mantenha o objeto estacionário, longe da confusão, se possível.
 4. Opcionalmente, limpe a cache [de mapeamento espacial](https://docs.microsoft.com/windows/mixed-reality/spatial-mapping) no seu dispositivo HoloLens.
 5. Digitalize o objeto andando ao redor dele. Certifique-se de que a maior parte do objeto é observado.
 6. Desajei uma área de busca suficientemente grande para cobrir o objeto.
 7. O objeto deve permanecer estacionário durante a deteção.
 8. Inicie a deteção de objetos e visualize a renderização com base na pose estimada.
 9. Bloqueie o objeto detetado ou pare de rastrear uma vez que a pose seja estável e precisa para preservar a vida útil da bateria.

**Q: Quão precisa é uma pose estimada?**

**A:** Depende do tamanho do objeto, material, ambiente, etc. Para objetos pequenos, a pose estimada pode estar dentro de um erro de 2 cm. Para objetos grandes, como um carro, o erro pode chegar a 2-8 cm.

**P: As âncoras de objetos podem manusear objetos móveis?**

**A:** Não suportamos objetos **em movimento contínuo** ou **dinâmicos.**

**P: As âncoras de objetos podem lidar com deformações ou articulações?**

**A:** Parcialmente, dependendo da quantidade de formas ou geometria do objeto alterações devido à deformação ou articulação. Se a geometria do objeto mudar muito, o utilizador pode criar outro modelo para essa configuração e usá-lo para deteção.

**P: Quantos objetos diferentes podem detetar âncoras de objetos ao mesmo tempo?**

**A:** Atualmente, apoiamos a deteção de um único modelo de objeto de cada vez. 

**P: As âncoras de objetos podem detetar múltiplas instâncias do mesmo modelo de objeto?**

**A:** Sim, pode detetar até 3 objetos do mesmo tipo de modelo. A aplicação pode ligar `ObjectObserver.DetectAsync` várias vezes com diferentes consultas para detetar várias instâncias do mesmo modelo.

**P: O que devo fazer se o tempo de execução das Âncoras de Objeto não conseguir detetar o meu objeto?**

**A:**

* Certifique-se de que o quarto tem texturas suficientes adicionando alguns cartazes.
* Digitalize o objeto mais completamente.
* Ajuste os parâmetros do modelo como descrito acima.
* Forneça uma caixa de encadernamento apertado como área de pesquisa que inclua a a maioria ou a maioria do objeto.
* Limpe a cache de mapeamento espacial e rescana o objeto.
* Capture diagnósticos e envie os dados para nós.

**P: Como escolher parâmetros de consulta de objetos?**

**A:**

* Forneça áreas de pesquisa apertadas para cobrir idealmente todo o objeto para melhorar a velocidade e precisão de deteção.
* O padrão `ObjectQuery.MinSurfaceCoverage` do modelo de objeto geralmente é bom, caso contrário use um valor menor para obter uma deteção mais rápida.
* Utilize um pequeno valor para `ObjectQuery.ExpectedMaxVerticalOrientationInDegrees` se espera que o objeto seja de cima para cima.
* Uma aplicação deve sempre utilizar um `1:1` modelo de objeto para deteção. A escala estimada deve ser próxima de 1 idealmente dentro de 1% de erro. Uma aplicação poderia definir `ObjectQuery.MaxScaleChange` ou `0` `0.1` desativar ou ativar a estimativa de escala, e avaliar qualitativamente a pose do caso.

**P: Como consigo obter dados de diagnóstico de Object Anchors dos HoloLens?**

**A:** A aplicação pode especificar a localização dos arquivos de diagnóstico. A aplicação de amostra Object Anchors escreve diagnósticos para a pasta **TempState.**

**P: Porque é que o modelo de origem não se alinha com o objeto físico ao utilizar a pose devolvida pelo Object Anchors Unitity SDK?**

**A:** A unidade pode alterar o sistema de coordenadas ao importar um modelo de objeto. Por exemplo, o Object Anchors Unitity SDK inverte o eixo Z ao converter de um sistema de coordenadas destro para canhoto, mas a Unidade pode aplicar uma rotação adicional sobre o eixo X ou Y. Um desenvolvedor pode determinar esta rotação adicional visualizando e comparando os sistemas de coordenadas.

**P: Apoia o 2D?**

**A:** Como somos baseados em geometria, só apoiamos o 3D.

**P: Pode diferenciar entre o mesmo modelo em cores diferentes?**

**A:** Como os nossos algoritmos são baseados em geometria, cores diferentes do mesmo modelo não se comportarão de forma diferente durante a deteção.

**P: Posso usar Âncoras de Objetos sem conectividade com internet?**

**A:** 
* Para a ingestão e treino do modelo, a conectividade é necessária, uma vez que isso ocorre na nuvem.
* As sessões de tempo de execução estão totalmente no dispositivo e não requerem conectividade, uma vez que todos os cálculos ocorrem nos HoloLens 2.

## <a name="privacy-faq"></a>FAQ de privacidade
**P: Como é que o Azure Object Anchors armazena dados?**

**A:** Armazenamos apenas metadados do sistema, que são encriptados em repouso com uma chave de encriptação de dados gerida pela Microsoft.
