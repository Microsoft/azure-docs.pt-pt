---
title: Propriedades de marketing
description: Uma descrição dos diferentes campos de marketing recolhidos no portal e como eles vão aparecer no catálogo de Dispositivos Certificados Azure
author: nikuntjo
ms.author: nikuntjo
ms.service: certification
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: template-concept
ms.openlocfilehash: 45ecab18451ca0915ee3891278db47feb7469915
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969547"
---
# <a name="marketing-properties"></a>Propriedades de marketing

No processo de adição de detalhes do [seu dispositivo,](tutorial-02-adding-device-details.md)será necessário fornecer informações de marketing que serão exibidas no catálogo de [Dispositivos Certificados Azure](https://devicecatalog.azure.com). Estas informações são recolhidas dentro do portal Azure Certified Device durante o processo de submissão da certificação e serão utilizadas como parâmetros de filtro no catálogo. Este artigo fornece um mapeamento entre os campos recolhidos no portal para a forma como aparecem no catálogo. Após a leitura deste artigo, os parceiros devem entender melhor que informações fornecer durante o processo de certificação para melhor representar o seu produto no catálogo.

![Visão geral do PDP](./media/concepts-marketing/pdp-overview.png)

## <a name="azure-certified-device-catalog-product-tile"></a>Azulejo de catálogo de dispositivo certificado Azure

Os visitantes do catálogo interagirão primeiro com o seu dispositivo como um azulejo de produto de catálogo na página de pesquisa. Isto fornecerá uma visão geral básica do dispositivo e certificações que foi premiado.

![Modelo de azulejo do produto](./media/concepts-marketing/product-tile.png)

| Campo | Descrição                  | Onde adicionar no portal                |
|---------------|-------------------------|----------------------------------|
| Nome do Dispositivo | Nome público do seu dispositivo certificado         | Separador básico dos detalhes do dispositivo|
| Nome da empresa| Nome público da sua empresa  | Não editável no portal. Extraído do nome da conta MPN |
| Foto do produto  | Imagem do seu dispositivo com resolução mínima 200p x 200p  | Detalhes de marketing |
| Classificação de certificação  | Etiqueta de certificação obrigatória de dispositivo certificado Azure e crachás de certificação opcional  | Separador básico dos detalhes do Dispositivo. Deve passar nos testes apropriados na secção de teste de Connect &. |

## <a name="product-description-page-information"></a>Informações da página de descrição do produto

Uma vez que um cliente tenha clicado no seu dispositivo a partir da página de pesquisa do catálogo, eles serão navegados para a página de descrição do produto do seu dispositivo. É aqui que será encontrada a maior parte das informações fornecidas durante o processo de certificação.

A página de descrição do produto realça características-chave, algumas das quais já foram utilizadas para o azulejo do produto.

![Barra superior PDP](./media/concepts-marketing/pdp-top.png)

| Campo | Descrição                  | Onde adicionar no portal                |
|---------------|-------------------------|----------------------------------|
| Classe de dispositivo | Classificação do fator de forma e principal finalidade do seu dispositivo[(Saiba mais)](./resources-glossary.md)       | Separador básico dos detalhes do dispositivo|
| Tipo de Dispositivo | Classificação do dispositivo com base na prontidão de implementação[(Saiba mais)](./resources-glossary.md) | Separador básico dos detalhes do dispositivo |
| Geo disponibilidade | Regiões que o seu dispositivo está disponível para compra  | Detalhes de marketing |
| Sistemas operativos  | Sistemas operativos que o seu dispositivo suporta  | Separador de detalhes do produto dos detalhes do Dispositivo |
| Indústrias-alvo  | Top 3 indústrias para as qual o seu dispositivo está otimizado  | Detalhes de marketing |
| Product description  | Campo de texto gratuito para que possa escrever a sua descrição de marketing do seu produto. Isto pode capturar detalhes não listados no portal, ou adicionar contexto adicional para os benefícios de usar o seu dispositivo. | Detalhes de marketing|

O restante da página está focado em apresentar as especificações técnicas do seu dispositivo em formato de mesa que ajudarão o seu cliente a entender melhor o seu produto. Por conveniência, as informações apresentadas no topo da página também estão listadas aqui. O resto da tabela é seccionado pelos componentes especificados no portal.

![Página inferior do PDP](./media/concepts-marketing/pdp-bottom.png)

| Campo | Descrição                  | Onde adicionar no portal                |
|---------------|-------------------------|----------------------------------|
| Tipo de componente | Classificação do fator de forma e principal finalidade do seu dispositivo[(Saiba mais)](./resources-glossary.md)       | Detalhes do produto dos detalhes do dispositivo|
| Nome do componente| Nome do componente que está descrevendo | Detalhes do produto dos detalhes do dispositivo |
| Informação adicional sobre componentes | Especificações adicionais de hardware, tais como sensores incluídos, conectividade, aceleradores, etc.  | Informações adicionais sobre os componentes dos detalhes do dispositivo[(Saiba mais)](./how-to-using-the-components-feature.md)  |
| Texto de dependência do dispositivo | Texto fornecido pelo parceiro que descreve as diferentes dependências que o produto requer para se conectar ao Azure[(Saiba mais)](./how-to-indirectly-connected-devices.md)   | Secção de comentários voltados para o cliente do separador Dependencies dos detalhes do Dispositivo |
| Ligação de dependência do dispositivo  | Ligação a um dispositivo certificado que o seu produto atual requer  | Separador de dependências de detalhes do Dispositivo |

## <a name="shop-links"></a>Links de loja
Disponível tanto no azulejo do produto como na página de descrição do produto é um botão Shop. Quando clicado pelo cliente, abre-se uma janela que lhes permite selecionar um distribuidor (é-lhe permitido listar até 5 distribuidores). Uma vez selecionado, o cliente é redirecionado para o URL fornecido pelo parceiro.

![Imagem da experiência pop-up da Loja](./media/concepts-marketing/shop.png)

| Campo | Descrição                  | Onde adicionar no portal                |
|---------------|-------------------------|----------------------------------|
| Nome do distribuidor | Nome do distribuidor que está a vender o seu produto | Detalhes de marketing|
| Obter Dispositivo| Link para site externo para o cliente comprar o dispositivo (ou solicitar uma cotação do distribuidor). Isto pode ser o mesmo que a página do Fabricante se o distribuidor for o mesmo que o fabricante do dispositivo. Se uma página de compra não estiver disponível, esta será redirecionando para a página do distribuidor para que o cliente os contacte diretamente.  | URL da página do produto distribuidor em detalhes de marketing. Se não houver página de compra disponível, o link será por defeito para o URL do Distribuidor em detalhes de Marketing. |

## <a name="external-links"></a>Ligações externas
Também incluídos na página Descrição do Produto estão links que navegam para sites ou ficheiros fornecidos pelo parceiro que ajudam o cliente a entender melhor o produto. Aparecem para o topo da página, por baixo do texto de descrição do produto. Os links apresentados diferirão para diferentes tipos de dispositivos e programas de certificação.

| Ligação | Description                  | Onde adicionar no portal                |
|---------------|-------------------------|----------------------------------|
| Começar guia* | Ficheiro PDF com instruções do utilizador para ligar e utilizar o seu dispositivo com os serviços Azure | Adicionar secção de guia 'Get Started' do portal|
| Página do fabricante*|Link para a página do fabricante. Esta página pode ser a página específica do produto para o seu dispositivo, ou para a página inicial da empresa se não estiver disponível uma página de marketing. | Página de marketing do fabricante em detalhes de Marketing |
| Modelo do dispositivo | Modelos DTDL públicos para soluções IoT Plug e Play  | Não editável no portal. O modelo do dispositivo deve ser carregado para o[repositório de modelos públicos](https://aka.ms/modelrepo)  |
| Código-fonte do dispositivo | URL para o código de origem do dispositivo para tipos de dispositivos Dev Kit| Separador básico dos detalhes do dispositivo  |

 **Necessário para todos os dispositivos publicados*

## <a name="next-steps"></a>Passos seguintes
Agora que tem uma noção de como utilizamos as informações que fornece durante a certificação, está agora pronto para certificar o seu dispositivo! Inicie o seu projeto de certificação ou volte à fase de detalhes do dispositivo para adicionar a sua própria informação de marketing.

- [Inicie a sua jornada de certificação](./tutorial-00-selecting-your-certification.md)
- [Adicionar detalhes do dispositivo](./tutorial-02-adding-device-details.md)
