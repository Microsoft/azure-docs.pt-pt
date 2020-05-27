---
title: Autenticar runbooks da Automatização do Azure com o Amazon Web Services
description: Este artigo diz como autenticar livros com a Amazon Web Services.
keywords: autenticação aws, configurar aws
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: acb056fb959fda320a14059e2b36a0f5e3fb0b37
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837195"
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Autenticar runbooks com o Amazon Web Services

Automatizar tarefas comuns com recursos nos Amazon Web Services (AWS) pode ser efetuado com runbooks de Automatização no Azure. Pode automatizar muitas tarefas no AWS através de runbooks de Automatização, tal como o pode fazer com recursos no Azure. Para autenticação, deve ter uma assinatura Azure.

## <a name="obtain-aws-subscription-and-credentials"></a>Obtenha subscrição e credenciais AWS

Para autenticar com AWS, deve obter uma subscrição AWS e especificar um conjunto de credenciais AWS para autenticar os seus livros de execução que funcionam a partir da Azure Automation. As credenciais específicas necessárias são a Chave de Acesso AWS e a Chave Secreta. Ver [utilizar credenciais AWS](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).

## <a name="configure-automation-account"></a>Configurar conta de Automatização

Pode utilizar uma conta de Automação existente para autenticar com AWS. Em alternativa, pode dedicar uma conta para livros de execução direcionados para os recursos DaWS. Neste caso, crie uma nova [conta Automation.](automation-create-standalone-account.md)  

## <a name="store-aws-credentials"></a>Armazenar credenciais AWS

Deve armazenar as credenciais AWS como ativos na Azure Automation. Consulte a Gestão das Chaves de [Acesso para a sua Conta AWS](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) para obter instruções sobre a criação da Chave de Acesso e da Chave Secreta. Quando as teclas estiverem disponíveis, copie o ID da chave de acesso e o ID da chave secreta num local seguro. Pode descarregar o seu ficheiro chave para guardá-lo em algum lugar seguro.

## <a name="create-credential-asset"></a>Criar ativo credencial

Depois de ter criado e copiado as suas chaves de segurança AWS, tem de criar um ativo credencial com a conta Automation. O ativo permite-lhe armazenar as teclas AWS de forma segura e remeti-las nos seus livros de execução. Ver [Criar um novo ativo credencial com o portal Azure.](shared-resources/credentials.md#create-a-new-credential-asset-with-the-azure-portal) Introduza as seguintes informações da AWS nos campos fornecidos:
    
* **Nome**  -  **AWScred**, ou um valor apropriado seguindo os seus padrões de nomeação
* **Nome do utilizador** - O seu ID de acesso
* **Palavra-passe** - Nome da sua Chave Secreta 

## <a name="next-steps"></a>Passos seguintes

* Para aprender a criar livros de corridas para automatizar tarefas em AWS, consulte Implementar um VM amazon [Web Services com um livro de execução](automation-scenario-aws-deployment.md).