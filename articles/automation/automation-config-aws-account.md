---
title: Autenticar runbooks da Automatização do Azure com o Amazon Web Services
description: Este artigo descreve como criar e validar uma credencial AWS para runbooks na Automatização do Azure gerindo recursos AWS.
keywords: autenticação aws, configurar aws
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 1bf60d17af1d9866de6a62ac538fa0bd9a15ce52
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82113396"
---
# <a name="authenticate-azure-automation-runbooks-with-amazon-web-services"></a>Autenticar runbooks da Automatização do Azure com o Amazon Web Services

Automatizar tarefas comuns com recursos nos Amazon Web Services (AWS) pode ser efetuado com runbooks de Automatização no Azure. Pode automatizar muitas tarefas no AWS através de runbooks de Automatização, tal como o pode fazer com recursos no Azure. Apenas são necessárias duas coisas:

* Uma subscrição do AWS e um conjunto de credenciais. Mais especificamente, a Chave de Acesso do AWS e a Chave Secreta. Para obter mais informações, veja o artigo [Using AWS Credentials (Com Credenciais AWS)](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).
* Uma subscrição do Azure e a conta de Automatização.

Para efetuar a autenticação com o AWS, tem de especificar um conjunto de credenciais do AWS para autenticar os runbooks a partir da Automatização do Azure. Se já tiver uma conta de Automatização criada e pretende utilizá-la para efetuar a autenticação com o AWS, pode seguir os passos na secção seguinte. Se quiser dedicar uma conta para livros de execução direcionados para os recursos DaWS, primeiro deve criar uma nova [conta Automation](automation-create-standalone-account.md) e saltar o passo para criar uma conta Run As. Depois de criar a conta, siga os passos abaixo para completar a configuração.

## <a name="configure-automation-account"></a>Configurar conta de Automatização

Para a Automatização do Azure comunicar com o AWS, primeiro tem de obter as suas credenciais do AWS e armazená-las como recursos na Automatização do Azure. Execute os seguintes passos documentados no documento AWS [Gerir Chaves de Acesso para a sua Conta AWS](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) para criar uma Chave de Acesso e copiar o **ID da Chave de Acesso** e **Chave de Acesso Secreta** (opcionalmente, transfira o ficheiro de chave para o armazenar num local seguro).

Depois de ter criado e copiado as chaves de segurança AWS, tem de criar um recurso de Credencial com uma conta de Automatização do Azure para as armazenar em segurança as e referenciar com os seus runbooks. Siga os passos na secção: **Para criar uma nova credencial** nos ativos credenciais no artigo da [Automação Azure](shared-resources/credentials.md#create-a-new-credential-asset-with-the-azure-portal) e introduza as seguintes informações:

1. Na caixa **Nome**, introduza **AWScred** ou um valor adequado a seguir às normas de nomenclatura.
2. Na caixa de **nomes do Utilizador,** digite o id **de acesso** e a chave de **acesso secreto** na **palavra-passe** e confirme a caixa **de palavra-passe.**

## <a name="next-steps"></a>Passos seguintes

* Leia o artigo de solução [Automating deployment of a VM in Amazon Web Services (Automatizar a implementação de uma VM nos Amazon Web Services)](automation-scenario-aws-deployment.md) para saber como criar runbooks para automatizar as tarefas no AWS.
