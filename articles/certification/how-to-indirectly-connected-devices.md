---
title: Pacotes de dispositivos certificados e dispositivos indiretamente ligados
titleSuffix: Azure Certified
description: Veja como enviar um dispositivo indiretamente ligado para certificação.
author: cbroad
ms.author: cbroad
ms.date: 02/23/2021
ms.topic: how-to
ms.service: certification
ms.openlocfilehash: da3110b562bc5ddbd37657f31cbdd3790a13b897
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969467"
---
# <a name="device-bundles-and-indirectly-connected-devices"></a>Pacotes de dispositivos e dispositivos indiretamente ligados

Para suportar dispositivos que interagem com o Azure através de um dispositivo, ofertas SaaS ou PaaS, o nosso portal de submissão – https://www.certify.azure.com) e catálogo de dispositivos – permitem que https://devicecatalog.azure.com) conceitos de agregação e dependências promovam e permitam o acesso a estas combinações de dispositivos ao nosso programa Azure Certified Device.

Dependendo da sua linha de produtos e serviços oferecidos, a sua situação pode exigir uma combinação destes passos:


![Criar dependências de projetos](./media/indirect-connected-device/picture-1.png )
## <a name="sensors-and-indirect-devices"></a>Sensores e dispositivos indiretos
Muitos sensores requerem um dispositivo para ligar ao Azure. Além disso, poderá ter vários dispositivos compatíveis que irão funcionar com o dispositivo sensor. **Para acomodar estes cenários, é necessário certificar primeiro o(s) dispositivo(s) antes de certificar o sensor que irá passar informações através dos mesmos.**

Matriz de exemplo de combinações de submissão ![ Exemplo de submissão](./media/indirect-connected-device/picture-2.png )

Para certificar o seu sensor, que requer um dispositivo separado:
1.  Em primeiro lugar, [certifique o dispositivo](https://certify.azure.com) e publique no Catálogo de Dispositivos Certificados Azure
    - Se tiver vários dispositivos passthrough compatíveis (como no exemplo acima), submeta-os separadamente para certificação e publique também no catálogo
2.  Com o sensor ligado através do dispositivo, submeta o sensor para certificação
    * No separador "Dependências&quot; da secção &quot;Detalhes do Dispositivo&quot;, desaver os seguintes valores
        * Tipo de dependência = &quot;Gateway de hardware&quot;
        * URL de dependência = &quot;ligação URL ao dispositivo no catálogo do dispositivo&quot;
        * Usado durante os testes = &quot;Sim&quot;
        * Adicione quaisquer comentários virados para o Cliente que devem ser fornecidos a um utilizador que veja a descrição do produto no catálogo do dispositivo. (exemplo: &quot;São necessários dispositivos da Série 100 para que os sensores se conectem ao Azure")

3.  Se tiver mais dispositivos que gostaria de adicionar como opcional para este dispositivo, pode selecionar "+ Adicionar dependência adicional". Em seguida, siga a mesma orientação e note que não foi utilizado durante os testes. Nos comentários voltados para o Cliente, certifique-se de que os seus clientes estão cientes de que outros dispositivos estão associados a este sensor (como alternativa ao dispositivo que foi utilizado durante o teste).

![Texto alt](./media/indirect-connected-device/picture-3.png "Tipo de dependência de hardware")

## <a name="paas-and-saas-offerings"></a>Ofertas de PaaS e SaaS
Como parte do seu portfólio de produtos, pode ter dispositivos que certifica, mas o seu dispositivo também requer outros serviços da sua empresa ou de outras empresas terceiras. Para adicionar esta dependência, siga estes passos:
1. Inicie o processo de submissão do seu dispositivo
2. No separador "Dependências", desa um conjunto dos seguintes valores
    - Tipo de dependência = "Serviço de software"
    - Nome de serviço = "[o nome do seu produto]"
    - URL de dependência = "URL link para uma página de produto que descreve o serviço"
    - Adicione todos os comentários que deverão ser fornecidos a um utilizador que veja a descrição do produto no Catálogo de Dispositivos Certificados Azure
3. Se tiver outros softwares, serviços ou dependências de hardware que gostaria de adicionar como opcional para este dispositivo, pode selecionar "+ Adicionar dependência adicional" e seguir a mesma orientação.

![Tipo de dependência de software](./media/indirect-connected-device/picture-4.png )

## <a name="bundled-products"></a>Produtos agregados
As listas de produtos agregados são simplesmente a certificação bem sucedida de um dispositivo com outro componente que será vendido como parte do pacote numa lista de produtos. Tem a capacidade de submeter um dispositivo que inclua componentes extra, como um sensor de temperatura e um sensor de câmara (#1) ou pode submeter um sensor de toque que inclua um dispositivo passthrough (#2). Através da função "Componente", tem a capacidade de adicionar vários componentes à sua listagem.

Se pretender fazê-lo, formata a imagem de listagem de produtos para indicar que este produto vem com outros componentes.  Além disso, se o seu pacote necessitar de serviços adicionais para certificar, você precisará identificá-los através da dependência dos serviços.
Matriz de exemplo de produtos agregados

![Exemplo de submissão de pacotes](./media/indirect-connected-device/picture-5.png )

Para obter uma descrição mais detalhada sobre como utilizar a funcionalidade do componente no portal Azure Certified Device, consulte [a documentação da](./how-to-using-the-components-feature.md)nossa ajuda. 

Se um dispositivo for um dispositivo passthrough com um sensor separado no mesmo produto, crie um componente para refletir o dispositivo passthrough e outro componente para refletir o sensor. Os componentes podem ser adicionados ao seu projeto no separador detalhes do Produto da secção de detalhes do Dispositivo:

![Adicionar componentes](./media/indirect-connected-device/picture-6.png )

Para o dispositivo passthrough, desaperte o tipo componente como produto pronto para o cliente e preencha os outros campos conforme relevante para o seu produto. Exemplo:

![Detalhes do componente](./media/indirect-connected-device/picture-7.png )

Para o sensor, adicione um segundo componente, definindo o tipo componente como método periférico e de acessório como Discreto. Exemplo:

![Segundo componente](./media/indirect-connected-device/picture-8.png )

Uma vez criado o componente Sensor, edite os detalhes, navegue no separador Sensores e adicione os detalhes do sensor. Exemplo:

![Detalhes do sensor](./media/indirect-connected-device/picture-9.png )

Complete os detalhes dos seus projetos e envie o seu dispositivo para certificação normal.

