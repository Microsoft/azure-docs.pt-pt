---
title: Visão geral de Azure Key Vault-Azure Key Vault | Microsoft Docs
description: O Azure Key Vault é um serviço cloud que funciona como um arquivo de segredos seguro.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: overview
ms.custom: mvc
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 97df5c309a3bdbf5055d67f893e33ab1a0c3232a
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832762"
---
# <a name="what-is-azure-key-vault"></a>O que é o Azure Key Vault?

Azure Key Vault ajuda a resolver os seguintes problemas:

- **Gestão de Segredos** – O Azure Key Vault pode ser utilizado para armazenar de forma segura e controlar totalmente o acesso aos tokens, palavras-passe, certificados, chaves de API e outros segredos
- **Gestão de Chaves** – O Azure Key Vault também pode ser utilizado como uma solução de Gestão de Chaves. O Azure Key Vault torna mais fácil criar e controlar as chaves de encriptação utilizadas para encriptar os seus dados. 
- O **Gerenciamento de certificado** -Azure Key Vault também é um serviço que permite que você provisione, gerencie e implante com facilidade certificados TLS/SSL (segurança de camada de transporte/protocolo SSL) para uso com o Azure e seus recursos internos conectados. 
- **Armazenar segredos apoiados por módulos de segurança de hardware** -os segredos e as chaves podem ser protegidos por software ou FIPS 140-2 nível 2 com HSMs validados

## <a name="why-use-azure-key-vault"></a>Porquê utilizar o Azure Key Vault?

### <a name="centralize-application-secrets"></a>Centralizar os segredos das aplicações

Ao centralizar o armazenamento dos segredos das aplicações no Azure Key Vault, pode controlar a distribuição dos mesmos. O Key Vault reduz grandemente as possibilidades de haver fugas de segredos acidentais. Se o Key Vault for utilizado, os programadores de aplicações já não precisam de armazenar informações de segurança nas aplicações. Não ter de armazenar informações de segurança em aplicativos elimina a necessidade de tornar essa informação parte do código. Por exemplo, uma aplicação poderá ter de se ligar a uma base de dados. Em vez de armazenar a cadeia de conexão no código do aplicativo, você pode armazená-la com segurança no Key Vault.

Seus aplicativos podem acessar com segurança as informações de que precisam usando URIs. Esses URIs permitem que os aplicativos recuperem versões específicas de um segredo. Não é necessário escrever código personalizado para proteger qualquer informação secreta armazenada no Key Vault.

### <a name="securely-store-secrets-and-keys"></a>Guarde os segredos e chaves em segurança

Os segredos e as chaves são salvaguardadas pelo Azure, utilizando os algoritmos de norma da indústria, os comprimentos de chave e os módulos de segurança de hardware (HSMs). Os HSMs têm validação da norma federal norte-americana Federal Information Processing Standards (FIPS) 140-2 Nível 2.

O acesso a um cofre de chaves requer autenticação e autorização adequadas antes de um “chamador” (utilizador ou aplicação) poder obter acesso. A autenticação estabelece a identidade do chamador, ao passo que a autorização determina as operações que aquele pode fazer.

A autenticação é feita através do Azure Active Directory. A autorização pode ser feita através do controlo de acesso baseado em funções (RBAC) ou de uma política de acesso do Key Vault. O RBAC é utilizado para lidar com a gestão dos cofres e a política de acesso do Key Vault é utilizada para o acesso aos dados armazenados nos cofres.

O Azure Key Vaults pode ser protegido por HSMs de software ou hardware. Em casos em que seja preciso mais segurança, pode importar ou gerar chaves nos módulos de segurança de hardware (HSMs) que nunca deixam o limite do HSM. A Microsoft usa módulos de segurança de hardware nCipher. Você pode usar as ferramentas do nCipher para mover uma chave de seu HSM para Azure Key Vault.

Por fim, o Azure Key Vault foi concebido para que a Microsoft não veja ou extraia os seus dados.

### <a name="monitor-access-and-use"></a>Monitorizar o acesso e a utilização

Depois de criar alguns Key Vaults, vai querer monitorizar como e quando é que as chaves e segredos são acedidos. Você pode monitorar a atividade habilitando o registro em log para seus cofres. Pode configurar o Azure Key Vault para:

- Arquivar numa conta de armazenamento.
- Transmitir em fluxo para um hub de eventos.
- Envie os logs para Azure Monitor logs.

Tem controlo sobre os seus registos, pode protegê-los ao restringir o acesso e também pode eliminar aqueles de que já não precisa.

### <a name="simplified-administration-of-application-secrets"></a>Administração simplificada de segredos de aplicações

Quando armazena dados valiosos, tem de executar vários passos. As informações de segurança devem ser protegidas, devem seguir um ciclo de vida e devem estar altamente disponíveis. O Azure Key Vault simplifica o processo de atender a esses requisitos:

- Remover a necessidade de conhecimento interno de módulos de segurança de hardware.
- Aumentar verticalmente com pouca antecedência, para satisfazer os picos de utilização da sua organização.
- Replicar os conteúdos do seu cofre de chaves numa região e para uma região secundária. A replicação de dados garante alta disponibilidade e elimina a necessidade de qualquer ação do administrador para disparar o failover.
- Disponibilizar opções de administração padrão do Azure através do portal, da CLI do Azure e do PowerShell.
- Automatizar determinadas tarefas relativas a certificados que comprar junto de ACs públicas, como inscrição e renovação.

Além disso, o Azure Key Vault permite-lhe segregar segredos das aplicações. Os aplicativos podem acessar apenas o cofre que eles têm permissão para acessar, e eles podem ser limitados somente a executar operações específicas. Pode criar um cofre de chaves do Azure por aplicação e restringir os segredos armazenados num cofre a uma aplicação e a uma equipa de programadores específica.

### <a name="integrate-with-other-azure-services"></a>Integrar com outros serviços do Azure

Como um repositório seguro no Azure, o Key Vault foi usado para simplificar cenários como:
-  [Azure Disk Encryption](../security/fundamentals/encryption-overview.md)
-  A funcionalidade [Always Encrypted]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) no SQL Server e no banco de dados SQL do Azure
- [Serviço de Azure app]( https://docs.microsoft.com/azure/app-service/configure-ssl-certificate). 

O próprio Key Vault pode ser integrado em Contas de Armazenamento, em Hubs de Eventos e no Log Analytics.

## <a name="next-steps"></a>Passos seguintes

- [Quickstart: Create an Azure Key Vault using the CLI](quick-create-cli.md) (Início Rápido: Criar um Cofre de Chaves do Azure com a CLI do Azure)
- [Configure an Azure web application to read a secret from Key vault](tutorial-web-application-keyvault.md) (Configurar uma aplicação Web do Azure para ler segredos em cofres de segurança)
