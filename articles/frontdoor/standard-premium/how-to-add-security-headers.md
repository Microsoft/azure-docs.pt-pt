---
title: Configure cabeçalhos de segurança com Azure Front Door Standard/Premium (Preview) Conjunto de regras
description: Este artigo fornece orientações sobre como usar a regra definida para configurar cabeçalhos de segurança.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: c08ba57f43969bb2f0ee9c66b6cb4e92879ed258
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099902"
---
# <a name="configure-security-headers-with-azure-front-door-standardpremium-preview-rule-set"></a>Configure cabeçalhos de segurança com Azure Front Door Standard/Premium (Preview) Conjunto de regras

> [!Note]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [aqui](../front-door-overview.md).

Este artigo mostra como implementar cabeçalhos de segurança para prevenir vulnerabilidades baseadas no navegador como HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy ou X-Frame-Options. Os atributos baseados em segurança também podem ser definidos com cookies.

O exemplo a seguir mostra-lhe como adicionar um cabeçalho de Política de Segurança de Conteúdo a todos os pedidos que correspondam ao caminho na Rota. Aqui, apenas permitimos que scripts do nosso site de confiança, **https://apiphany.portal.azure-api.net** para executar na nossa aplicação.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

* Antes de configurar os cabeçalhos de segurança, tem primeiro de criar uma porta frontal. Para obter mais informações, veja [Quickstart: Create a Front Door](create-front-door-portal.md) (Início Rápido: Criar um Front Door).
* Reveja como [configurar uma Definição de Regras](how-to-configure-rule-set.md) se ainda não tiver utilizado a função 'Conjunto de Regras'.

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Adicione um cabeçalho de Política de Segurança de Conteúdo no portal Azure

1. Aceda ao perfil Azure Front Door Standard/Premium e selecione **Conjunto de Regras** em **Definições.**

1. **Selecione Adicionar** para adicionar um novo conjunto de regras. Dê à Regra Definir um **Nome** e, em seguida, forneça um **nome** para a regra. **Selecione Adicione uma Ação** e, em seguida, selecione O **Cabeçalho de Resposta**.

1. Desaprote o operador ao **Append** para adicionar este cabeçalho como resposta a todos os pedidos de entrada para esta rota.

1. Adicione o nome do cabeçalho: **Política de Segurança de Conteúdo** e defina os valores que este cabeçalho deve aceitar. Neste cenário, escolhemos *"script-src https://apiphany.portal.azure-api.net 'self'".*

1. Depois de ter adicionado todas as regras que pretende à sua configuração, não se esqueça de associar a regra definida a uma rota. Este passo é *necessário* para permitir que a regra definida tome medidas. 

> [!NOTE]
> Neste cenário, não acrescentámos [condições de jogo](concept-rule-set-match-conditions.md) à regra. Todos os pedidos de entrada que correspondam ao caminho definido na rota associada terão esta regra aplicada. Se quiser que se aplique apenas a um subconjunto desses pedidos, certifique-se de adicionar as suas **condições** específicas de correspondência a esta regra.

## <a name="clean-up-resources"></a>Limpar os recursos

### <a name="deleting-a-rule"></a>Apagar uma regra

Nos passos anteriores, configuraste o cabeçalho de Política de Segurança de Conteúdo com o conjunto de Regras. Se já não pretender uma regra, pode selecionar o nome 'Conjunto de Regras' e, em seguida, selecionar Eliminar a regra. 

### <a name="deleting-a-rule-set"></a>Apagar um conjunto de regras

Se pretender eliminar um Conjunto de Regras, certifique-se de que o dissocia de todas as rotas antes de apagar. Para obter orientações detalhadas sobre a eliminação de um conjunto de regras, consulte para [configurar o seu conjunto de regras](how-to-configure-rule-set.md).

## <a name="next-steps"></a>Passos seguintes

Para aprender a configurar uma Firewall de Aplicação Web para a sua porta frontal, consulte [firewall de aplicação web e porta frontal](../../web-application-firewall/afds/afds-overview.md).
