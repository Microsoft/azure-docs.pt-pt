---
title: Criar ofertas do Marketplace | Azure Marketplace
description: Criar ofertas no Azure e AppSource Marketplaces usando o Portal do Cloud Partner
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pabutler
ms.openlocfilehash: 4aa4e154c99f20963485a98d1a21af4ae2b7a1b1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73828102"
---
# <a name="create-azure-marketplace-and-appsource-offers"></a>Criar ofertas do Azure Marketplace e do AppSource

Uma finalidade essencial da Portal do Cloud Partner é permitir que os editores criem (e, em seguida, publiquem) ofertas para os Marketplaces Microsoft Azure e AppSource.  Essa operação sempre começa com a seleção do tipo de oferta desejado no [menu nova oferta](../portal-tour/cpp-new-offer-menu.md).  Em resposta, a nova página de **oferta** apropriada é exibida para esse tipo de oferta.  Por exemplo, a imagem a seguir mostra a **nova** página de oferta padrão para um tipo de aplicativo do Azure.

![Página padrão da nova oferta](./media/new-offer-page.png)

Há duas seleções de guia disponíveis na BarraDeMenu horizontal exibida em direção à parte superior desta página: 
- Guia **Editor** – habilita a entrada de informações e o carregamento de ativos para a nova instância de oferta.  Essa guia é exibida por padrão.
- Guia **status** – fornece o status de publicação e lista quaisquer problemas de validação e de revisão. 

Ao criar uma oferta, você usa a guia **Editor** para inserir informações sobre essa oferta. 

## <a name="editing-operations"></a>Editando operações

A barra de ferramentas horizontal, localizada acima da área de entrada de dados, exibe os seguintes botões:

|   Button    |   Objetivo                                                          |
|   ------    |  --------                                                          |
| **Guarde**    | Salva as alterações de entrada de dados recentes.  Você deve salvar as alterações manualmente antes de sair da página ou suas alterações são perdidas. | 
| **Carte** | Descarta alterações de entrada de dados recentes (desde o último salvamento)             |
| **Comparar** | Compara o estado da oferta atual com a oferta publicada.  Habilitada somente após uma oferta ter sido publicada com êxito.  |
| **Publicar** | Inicia o processo de publicação para esta oferta                       |
| **Eliminar**  | Exclui esta oferta depois que ela é criada, mas antes de ser publicada. |
|   |   |


## <a name="editing-tabs"></a>Editando guias

Ao criar uma oferta, você fornece os dados necessários e opcionais em cada guia localizada na coluna vertical esquerda da nova página de **oferta** .  Controles de interface de usuário padrão, como caixas de texto, menus suspensos e caixas de seleção, são exibidos para coleta de dados.  Embora a coleção específica de guias de edição dependa do tipo de oferta, a tabela a seguir lista algumas das guias comuns.

|      Nome da guia       |   Objetivo                                                            |
|      --------       |   -------                                                            |
| **Configurações da oferta**  | Coleta informações de identidade do Publicador e oferta.                    |
| **SKU**            | Define as características técnicas e comerciais para cada versão de SKU (unidade de manutenção de estoque) de sua oferta |
| **Test Drive**      | Para os tipos que dão suporte a esse recurso opcional, o define uma demonstração para sua oferta.  Para obter mais informações, consulte [o que é Test Drive?](../test-drive/what-is-test-drive.md)  |
| **Marketplace** ou **vitrine** | Coleta cadeias de caracteres de texto, documentos e imagens usadas para listar a oferta no Marketplace |
| **Suporte**         | Coleta informações de contato para cliente, engenharia e suporte online  |
|  |  |

O conteúdo das guias com nomes semelhantes pode ser diferente entre os diferentes tipos de oferta.  Detalhes específicos da oferta dessas guias são fornecidos na seção "criar oferta" para cada tipo de oferta.


## <a name="next-steps"></a>Passos seguintes

Depois de criar e salvar uma oferta e antes ou depois de publicá-la, você poderá [exibir seu status](./cpp-view-status-offer.md).
