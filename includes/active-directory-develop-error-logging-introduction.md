---
author: mmacy
ms.author: marsma
ms.date: 12/16/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 53198c663d318a2eb47bcb3207939bbcb1fdd59c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98763477"
---
As aplicações da Microsoft Authentication Library (MSAL) geram mensagens de registo que podem ajudar a diagnosticar problemas. Uma aplicação pode configurar o registo com algumas linhas de código, e ter controlo personalizado sobre o nível de detalhe e se os dados pessoais e organizacionais são ou não registados. Recomendamos que crie uma chamada de registo MSAL e forneça uma forma de os utilizadores submeterem registos quando tiverem problemas de autenticação.

## <a name="logging-levels"></a>Níveis de registo

A MSAL fornece vários níveis de detalhes de registo registador:

- Erro: Indica que algo correu mal e foi gerado um erro. Usado para depurar e identificar problemas.
- Aviso: Não houve necessariamente um erro ou falha, mas destinam-se a diagnósticos e problemas de localização.
- Informações: A MSAL registará eventos destinados a fins informativos não necessariamente destinados à depuragem.
- Verbose: Padrão. MSAL regista todos os detalhes do comportamento da biblioteca.

## <a name="personal-and-organizational-data"></a>Dados pessoais e organizacionais

Por padrão, o madeireiro MSAL não captura quaisquer dados pessoais ou organizacionais altamente sensíveis. A biblioteca oferece a opção de ativar dados pessoais e organizacionais se decidir fazê-lo.

As secções seguintes fornecem mais detalhes sobre a sessão de erros da MSAL para a sua aplicação.