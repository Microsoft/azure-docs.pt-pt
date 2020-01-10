---
title: Configurar a autenticação com Amazon Web Services
description: Este artigo descreve como criar e validar uma credencial AWS para runbooks na Automatização do Azure gerindo recursos AWS.
keywords: autenticação aws, configurar aws
services: automation
ms.subservice: process-automation
ms.date: 04/17/2018
ms.topic: conceptual
ms.openlocfilehash: 596dc334a412b3e0839d7661a23af771e5cd7394
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75366945"
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Autenticar Runbooks com Amazon Web Services

Automatizar tarefas comuns com recursos nos Amazon Web Services (AWS) pode ser efetuado com runbooks de Automatização no Azure. Pode automatizar muitas tarefas no AWS através de runbooks de Automatização, tal como o pode fazer com recursos no Azure. Apenas são necessárias duas coisas:

* Uma subscrição do AWS e um conjunto de credenciais. Mais especificamente, a Chave de Acesso do AWS e a Chave Secreta. Para obter mais informações, veja o artigo [Using AWS Credentials (Com Credenciais AWS)](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).
* Uma subscrição do Azure e a conta de Automatização.

Para efetuar a autenticação com o AWS, tem de especificar um conjunto de credenciais do AWS para autenticar os runbooks a partir da Automatização do Azure. Se você já tiver uma conta de automação criada e quiser usá-la para autenticar com o AWS, você poderá seguir as etapas na seção a seguir: se desejar dedicar uma conta para runbooks direcionados a recursos do AWS, primeiro crie uma nova [conta de automação](automation-offering-get-started.md) (pule a opção para criar uma entidade de serviço) e use as seguintes etapas:

## <a name="configure-automation-account"></a>Configurar conta de Automatização

Para a Automatização do Azure comunicar com o AWS, primeiro tem de obter as suas credenciais do AWS e armazená-las como recursos na Automatização do Azure. Execute os seguintes passos documentados no documento AWS [Gerir Chaves de Acesso para a sua Conta AWS](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) para criar uma Chave de Acesso e copiar o **ID da Chave de Acesso** e **Chave de Acesso Secreta** (opcionalmente, transfira o ficheiro de chave para o armazenar num local seguro).

Depois de ter criado e copiado as chaves de segurança AWS, tem de criar um recurso de Credencial com uma conta de Automatização do Azure para as armazenar em segurança as e referenciar com os seus runbooks. Siga as etapas na seção: **para criar uma nova credencial** no artigo [ativos de credencial no Azure Automation](shared-resources/credentials.md#to-create-a-new-credential-asset-with-the-azure-portal) e insira as seguintes informações:

1. Na caixa **Nome**, introduza **AWScred** ou um valor adequado a seguir às normas de nomenclatura.
2. Na caixa **Nome de utilizador**, escreva o **ID de Acesso** e a **Chave de Acesso Secreta** na caixa **Palavra-passe** e **Confirmar palavra-passe**.

## <a name="next-steps"></a>Passos seguintes

* Leia o artigo de solução [Automating deployment of a VM in Amazon Web Services (Automatizar a implementação de uma VM nos Amazon Web Services)](automation-scenario-aws-deployment.md) para saber como criar runbooks para automatizar as tarefas no AWS.
