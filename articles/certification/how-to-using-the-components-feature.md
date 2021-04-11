---
title: Como utilizar a funcionalidade de componentes no portal Azure Certified Device
description: Um guia sobre a melhor utilização da função de componentes da secção de detalhes do Dispositivo para descrever com precisão o seu dispositivo
author: nikuntjo
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 091a666026853d677f1e9ed7e5e1736307cecbe8
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969452"
---
# <a name="add-components-on-the-portal"></a>Adicionar componentes no portal

Ao completar o [tutorial para adicionar detalhes](tutorial-02-adding-device-details.md) do dispositivo ao seu projeto de certificação, espera-se que descreva as especificações de hardware do seu dispositivo. Para tal, os utilizadores podem destacar vários produtos de hardware **separados (referidos** como componentes) que compõem o seu dispositivo. Isto permite-lhe promover melhor os dispositivos que vêm com hardware adicional, e permite que os clientes encontrem o produto certo pesquisando no catálogo com base nestas funcionalidades.

## <a name="prerequisites"></a>Pré-requisitos

- Deverá fazer o seu contrato e ter um projeto para o seu dispositivo criado no [portal Azure Certified Device](https://certify.azure.com). Para mais informações, consulte o [tutorial.](tutorial-01-creating-your-project.md)

## <a name="how-to-add-components"></a>Como adicionar componentes

Todos os projetos submetidos à certificação incluirão um componente **de Produto Pronto ao Cliente** (que em muitos casos representará o próprio produto holístico). Para melhor compreender a distinção de um tipo de componente de produto pronto para o cliente, consulte o nosso [glossário de certificação.](./resources-glossary.md) Todos os componentes adicionais estão ao seu critério para incluir para capturar com precisão o seu dispositivo.

1. Selecione `Add a component` no separador detalhes do Produto.

    ![Adicionar uma ligação de componente](./media/images/add-a-component-link.png)

1. Completos campos de formulário relevantes para o componente.

    ![Secção de detalhes do componente](./media/images/component-details-section.png)

1. Guarde as suas informações utilizando o `Save Product Details` botão na parte inferior da página:  

    ![Guardar botão de detalhes do produto](./media/images/save-product-details-button.png)

1. Depois de ter guardado o seu componente, poderá adaptar ainda mais as capacidades de hardware que suporta. Selecione o `Edit` link pelo nome do componente.  

    ![Editar botão componente](./media/images/component-edit.png)

1. Forneça informações relevantes sobre a capacidade do hardware, sempre que apropriado.  

    ![Imagem de secções de componentes editáveis](./media/images/component-selection-area.png)  

    Os campos de componentes editáveis (acima indicados) incluem:

    - **Geral**: Detalhes de hardware como processadores e hardware seguro
    - **Conectividade**: Opções de conectividade, protocolos e interfaces como rádio(s) e GPIO
    - **Aceleradores**: Especificar a aceleração do hardware como a GPU e a VPU
    - **Sensores**: Especificar sensores disponíveis, tais como GPS e vibração
    - **Especificações adicionais**: Informações adicionais sobre o dispositivo, tais como dimensões físicas e informações de armazenamento/bateria

1. Selecione `Save Product Details` na parte inferior da página de detalhes do Produto.

## <a name="component-use-requirements-and-recommendations"></a>Requisitos e recomendações de utilização de componentes

Pode ter dúvidas sobre quantos componentes incluir, ou que tipo de componente utilizar. Abaixo estão exemplos de alguns cenários de amostra de dispositivos que pode estar a certificar e como pode utilizar a funcionalidade de componentes.

| Tipo de Produto                                       | N.º Componentes | Componente 1 / Tipo de Acessório      | Componentes 2+ / Tipo de Acessório                    |
|----------------------------------------------------|------------|----------------------------------|--------------------------------------------------|
| Produto acabado                                   | 1          | Produto pronto para o cliente, discreto | N/D                                              |
| Produto acabado com **periférico destacável ou periférico** | 2 ou mais  | Produto pronto para o cliente, discreto | Periférico / Discreto ou Integrado              |
| Produto acabado com **componentes integrados**  | 2 ou mais  | Produto pronto para o cliente, discreto | Selecione o tipo apropriado / Discrete ou integrado |
| Kit de dev Solution-Ready                             | 2 ou mais  | Produto pronto para o cliente, discreto | Selecione o tipo apropriado / Discrete ou integrado |

## <a name="example-component-usage"></a>Exemplo de utilização de componentes

Abaixo estão exemplos de como um OEM chamado Contoso usaria a funcionalidade de componentes para certificar o seu produto, chamado Falcon.

1. O Falcon é um dispositivo autónomo completo que não se integra num produto maior.
    1. N.º de componentes: 1
    1. Tipo de dispositivo componente: Produto pronto para o cliente
    1. Tipo de acessório: Discreto

     ![Imagem do produto pronto para o cliente](./media/images/customer-ready-product.png)

1. O Falcon é um dispositivo que inclui um módulo integrado de câmara periférica fabricado pela INC Electronics que se conecta via USB ao Falcon.
    1. N.º de componentes: 2
    1. Tipo de dispositivo componente: Produto pronto para o cliente, periférico
    1. Tipo de acessório: Discreto, Integrado
    
    > [!Note]
    > O componente periférico é considerado integrado por não ser removível.

     ![Imagem de componente de exemplo periférico](./media/images/peripheral.png)

1. O Falcon é um dispositivo que inclui um Sistema Integrado de Módulo da INC Electronics que utiliza um processador incorporado Apollo52 da empresa Espressif e tem uma arquitetura ARM64.
    1. N.º de componentes: 2
    1. Tipo de dispositivo componente: Produto pronto para o cliente, sistema no módulo
    1. Tipo de acessório: Discreto, Integrado

    > [!Note]
    > O componente periférico é considerado integrado por não ser removível. O componente SoM também incluiria informações sobre o processador.

     ![Imagem do sistema no componente de exemplo do módulo ](./media/images/system-on-module.png)

## <a name="additional-tips"></a>Dicas adicionais

Fornecemos abaixo mais esclarecimentos sobre a nossa política de utilização de componentes. Se tiver alguma dúvida sobre o uso adequado do componente, contacte a nossa equipa [iotcert@microsoft.com](mailto:iotcert@microsoft.com) em , e teremos todo o gosto em ajudar!

1. Um projeto deve conter **apenas** um componente do Produto Pronto ao Cliente. Se estiver a certificar um projeto com dois dispositivos independentes, estes dispositivos devem ser certificados separadamente.
1. Cabe-lhe principalmente utilizar (ou não) componentes para promover as capacidades do seu dispositivo a potenciais clientes.
1. Durante a nossa revisão do seu dispositivo, a equipa de Certificação Azure apenas necessitará de pelo menos um componente do Produto Pronto ao Cliente para ser listado. No entanto, podemos solicitar edições à informação do componente se os detalhes não forem claros ou parecerem faltar (por exemplo, o fabricante de componentes não é fornecido para um tipo de Produto Pronto para o Cliente).

## <a name="next-steps"></a>Passos seguintes

Agora que está pronto para usar a funcionalidade de componentes, está agora pronto para completar os detalhes do seu dispositivo ou editar o seu projeto para maior clareza.

- [Tutorial: Adicionar detalhes do dispositivo](tutorial-02-adding-device-details.md)
- [Editar o seu dispositivo publicado](how-to-edit-published-device.md)

