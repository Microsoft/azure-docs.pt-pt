---
title: Glossário do programa Azure Certified Device
description: Uma lista de termos comuns usados no programa Azure Certified Device
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: conceptual
ms.date: 03/03/2021
ms.custom: template-concept
ms.openlocfilehash: f58c29c6bd2c22f37d2285ac6e93c6f54e47c4e0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305094"
---
# <a name="azure-certified-device-program-glossary"></a>Glossário do programa Azure Certified Device

Este guia fornece definições de termos comumente utilizados no programa e portal do Dispositivo Certificado Azure. Consulte este glossário para obter esclarecimentos sobre o processo de certificação. Para sua conveniência, este glossário é categorizado com base em grandes conceitos de certificação sobre os quais você pode ter dúvidas.

## <a name="device-class"></a>Classe de dispositivo

Ao criar o seu projeto de certificação, será solicitado que especifique uma classe de dispositivo. A classe do dispositivo refere-se ao fator de forma ou classificação que melhor representa o seu dispositivo.

- **Gateway**

    Um dispositivo que processa dados enviados por uma rede IoT.

- **Sensor**

    Um dispositivo que deteta e responde a alterações num ambiente e se conecta a gateways para processar as alterações.

- **Outro**

    Se selecionar Outro, adicione uma descrição da classe do seu dispositivo nas suas próprias palavras. Com o tempo, podemos continuar a adicionar novos valores a esta lista, especialmente porque continuamos a monitorizar o feedback dos nossos parceiros.

## <a name="device-type"></a>Tipo de Dispositivo

Também lhe será pedido que selecione um dos dois tipos de dispositivos durante o processo de certificação.

- **Produto acabado**

    Um dispositivo pronto para a solução e pronto para a implantação da produção. Normalmente num fator de forma acabada com firmware e um sistema operativo. Estes podem ser dispositivos de uso geral que requerem personalização adicional ou dispositivos especializados que não requerem modificações para uso.
- **Kit Dev pronto para a solução**

    Um kit de desenvolvimento contendo hardware e software ideal para prototipagem fácil, normalmente não num fator de forma finalizado. Normalmente inclui código de amostra e tutoriais para permitir prototipagem rápida.

## <a name="component-type"></a>Tipo de componente

Na secção de detalhes do Dispositivo, descreverá o seu dispositivo listando componentes por tipo de componente. Pode ver mais orientação sobre os componentes [aqui.](./how-to-using-the-components-feature.md)

- **Produto pronto para o cliente**

    Representação de componentes do dispositivo geral ou primário. Isto é diferente de um **Produto Acabado,** que é uma classificação do dispositivo como estando pronto para uso do cliente sem mais desenvolvimento. Um produto acabado conterá um componente de produto pronto para o cliente.
- **Conselho de Desenvolvimento**

    Seja uma placa integrada ou destacável com microprocessador para facilitar a personalização.
- **Periférico**

    Seja uma adição integrada ou destacável ao produto (como um acessório). Estes são normalmente dispositivos que se ligam ao dispositivo principal, mas não contribuem para as funções primárias do dispositivo. Em vez disso, proporciona funções adicionais. A memória, RAM, armazenamento, discos rígidos e CPUs não são considerados dispositivos periféricos (em vez disso devem ser listados em Especificações Adicionais do componente Produto Pronto para o Cliente).
- **Módulo Sistema-On**  

    Um circuito de nível de placa que integra uma função do sistema num único módulo.

## <a name="component-attachment-method"></a>Método de fixação de componentes

O método de fixação de componentes é outro detalhe de componentes que informa o cliente sobre como o componente é integrado no produto global.

- **Integrado**
 
    Refere-se quando um componente do dispositivo faz parte do chassis principal do produto. Isto refere-se mais frequentemente a um tipo de componente periférico que não pode ser removido do dispositivo.  
    Exemplo: Um sensor de temperatura integrado dentro de um chassis gateway.

- **Discreto**

    Refere-se quando um componente **não** faz parte do chassis principal do produto.  
    Exemplo: Um sensor de temperatura externo que deve ser ligado ao dispositivo.


## <a name="next-steps"></a>Passos seguintes

Este glossário irá guiá-lo através do processo de certificação do seu projeto no portal. Está agora pronto para começar o seu projeto!
- [Tutorial: Criar o seu projeto](./tutorial-01-creating-your-project.md)
