---
title: Configurar a autenticação com os Amazon Web Services
description: Este artigo descreve como criar e validar uma credencial AWS para runbooks na Automatização do Azure gerindo recursos AWS.
keywords: autenticação aws, configurar aws
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 04/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9cf2da02ed174d5deda7e126e657261af349ee2d
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477946"
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Autenticar Runbooks com Amazon Web Services

Automatizar tarefas comuns com recursos nos Amazon Web Services (AWS) pode ser efetuado com runbooks de Automatização no Azure. Pode automatizar muitas tarefas no AWS através de runbooks de Automatização, tal como o pode fazer com recursos no Azure. Apenas são necessárias duas coisas:

* Uma subscrição do AWS e um conjunto de credenciais. Mais especificamente, a Chave de Acesso do AWS e a Chave Secreta. Para obter mais informações, veja o artigo [Using AWS Credentials (Com Credenciais AWS)](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).
* Uma subscrição do Azure e a conta de Automatização.

Para efetuar a autenticação com o AWS, tem de especificar um conjunto de credenciais do AWS para autenticar os runbooks a partir da Automatização do Azure. Se já tiver uma conta de automatização criada e pretende utilizá-lo para autenticar com o AWS, pode seguir os passos na secção seguinte: Se pretender dedicar uma conta para runbooks direcionados para recursos do AWS, deve primeiro criar um novo [conta de automatização](automation-offering-get-started.md) (ignore a opção para criar um principal de serviço) e utilize os seguintes passos:

## <a name="configure-automation-account"></a>Configurar conta de Automatização

Para a Automatização do Azure comunicar com o AWS, primeiro tem de obter as suas credenciais do AWS e armazená-las como recursos na Automatização do Azure. Execute os seguintes passos documentados no documento AWS [Gerir Chaves de Acesso para a sua Conta AWS](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) para criar uma Chave de Acesso e copiar o **ID da Chave de Acesso** e **Chave de Acesso Secreta** (opcionalmente, transfira o ficheiro de chave para o armazenar num local seguro).

Depois de ter criado e copiado as chaves de segurança AWS, tem de criar um recurso de Credencial com uma conta de Automatização do Azure para as armazenar em segurança as e referenciar com os seus runbooks. Siga os passos na secção: **Para criar uma nova credencial** no [recursos de credencial na automatização do Azure](shared-resources/credentials.md#to-create-a-new-credential-asset-with-the-azure-portal) e introduza as seguintes informações:

1. Na caixa **Nome**, introduza **AWScred** ou um valor adequado a seguir às normas de nomenclatura.
2. Na caixa **Nome de utilizador**, escreva o **ID de Acesso** e a **Chave de Acesso Secreta** na caixa **Palavra-passe** e **Confirmar palavra-passe**.

## <a name="next-steps"></a>Passos Seguintes

* Leia o artigo de solução [Automating deployment of a VM in Amazon Web Services (Automatizar a implementação de uma VM nos Amazon Web Services)](automation-scenario-aws-deployment.md) para saber como criar runbooks para automatizar as tarefas no AWS.
