---
title: Descrição geral do Cofre de chaves do Azure - Cofre de chaves do Azure | Documentos da Microsoft
description: O Azure Key Vault é um serviço cloud que funciona como um arquivo de segredos seguro.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.assetid: 34af20ee-3fa7-4f28-9d98-6168b1759764
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: overview
ms.custom: mvc
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: 06f1a2b33b177cf667fc2d3c5932f7ff117d2bdc
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195477"
---
# <a name="what-is-azure-key-vault"></a>O que é o cofre de chave do Azure?

O Azure Key Vault ajuda a resolver os problemas seguintes:

- **Gestão de Segredos** – o Azure Key Vault pode ser utilizado para armazenar de forma segura e controlar totalmente o acesso a tokens, palavras-passe, certificados, chaves de API e outros segredos
- **Gestão de Chaves** – o Azure Key Vault também pode ser utilizado como uma solução de Gestão de Chaves. O Azure Key Vault torna mais fácil criar e controlar as chaves de encriptação utilizadas para encriptar os seus dados. 
- **Gestão de Certificados** – o Azure Key Vault também é um serviço que lhe permite aprovisionar, gerir e implementar facilmente certificados de Secure Sockets Layer/Transport Layer Security (SSL/TLS) públicos e privados para utilização com o Azure e os seus recursos ligados internos. 
- **Armazenar segredos protegidos por Módulos de Segurança de Hardware** – os segredos e as chaves podem ser protegidos por software ou HSMs validados por FIPS 140-2 Nível 2

## <a name="why-use-azure-key-vault"></a>Porquê utilizar o Azure Key Vault?

### <a name="centralize-application-secrets"></a>Centralizar os segredos das aplicações

Ao centralizar o armazenamento dos segredos das aplicações no Azure Key Vault, pode controlar a distribuição dos mesmos. O Key Vault reduz grandemente as possibilidades de haver fugas de segredos acidentais. Se o Key Vault for utilizado, os programadores de aplicações já não precisam de armazenar informações de segurança nas aplicações. Não ter de armazenar informações de segurança em aplicativos elimina a necessidade de fazer essas informações como parte do código. Por exemplo, uma aplicação poderá ter de se ligar a uma base de dados. Em vez de armazenar a cadeia de ligação no código do aplicativo, pode armazena de forma segura no Key Vault.

As aplicações com segurança podem aceder a informações de que precisam através de URIs. Estes URIs que permitem que os aplicativos obter versões específicas de um segredo. Não há necessidade de escrever código personalizado para proteger qualquer uma das informações secretas, armazenadas no Key Vault.

### <a name="securely-store-secrets-and-keys"></a>Guarde os segredos e chaves em segurança

Os segredos e as chaves são salvaguardadas pelo Azure, utilizando os algoritmos de norma da indústria, os comprimentos de chave e os módulos de segurança de hardware (HSMs). Os HSMs têm validação da norma federal norte-americana Federal Information Processing Standards (FIPS) 140-2 Nível 2.

O acesso a um cofre de chaves requer autenticação e autorização adequadas antes de um “chamador” (utilizador ou aplicação) poder obter acesso. A autenticação estabelece a identidade do chamador, ao passo que a autorização determina as operações que aquele pode fazer.

A autenticação é feita através do Azure Active Directory. A autorização pode ser feita através do controlo de acesso baseado em funções (RBAC) ou de uma política de acesso do Key Vault. O RBAC é utilizado para lidar com a gestão dos cofres e a política de acesso do Key Vault é utilizada para o acesso aos dados armazenados nos cofres.

O Azure Key Vaults pode ser protegido por HSMs de software ou hardware. Em casos em que seja preciso mais segurança, pode importar ou gerar chaves nos módulos de segurança de hardware (HSMs) que nunca deixam o limite do HSM. A Microsoft utiliza os módulos de hardware de segurança da Thales. Pode utilizar ferramentas da Thales para mover uma chave do seu HSM para o Azure Key Vault.

Por fim, o Azure Key Vault foi concebido para que a Microsoft não veja ou extraia os seus dados.

### <a name="monitor-access-and-use"></a>Monitorizar o acesso e a utilização

Depois de criar alguns Key Vaults, vai querer monitorizar como e quando é que as chaves e segredos são acedidos. Pode monitorizar a atividade ao ativar o registo para os cofres. Pode configurar o Azure Key Vault para:

- Arquivar numa conta de armazenamento.
- Transmitir em fluxo para um hub de eventos.
- Envie os registos para os registos do Azure Monitor.

Tem controlo sobre os seus registos, pode protegê-los ao restringir o acesso e também pode eliminar aqueles de que já não precisa.

### <a name="simplified-administration-of-application-secrets"></a>Administração simplificada de segredos de aplicações

Quando armazena dados valiosos, tem de executar vários passos. Informações de segurança tem de estar protegidas, tem de seguir um ciclo de vida, tem de ser altamente disponível. O Azure Key Vault simplifica o processo de cumprimento destes requisitos por:

- Eliminar a necessidade de conhecimentos de Módulos de Segurança de Hardware por parte dos utilizadores da sua organização
- Aumentar verticalmente com pouca antecedência, para satisfazer os picos de utilização da sua organização.
- Replicar os conteúdos do seu cofre de chaves numa região e para uma região secundária. Replicação de dados garante elevada disponibilidade e elimina a necessidade de qualquer ação do administrador para acionar a ativação pós-falha.
- Disponibilizar opções de administração padrão do Azure através do portal, da CLI do Azure e do PowerShell.
- Automatizar determinadas tarefas relativas a certificados que comprar junto de ACs públicas, como inscrição e renovação.

Além disso, o Azure Key Vault permite-lhe segregar segredos das aplicações. Aplicativos podem aceder apenas ao cofre que eles têm permissão para aceder e podem ser limitadas à realização de operações específicas. Pode criar um cofre de chaves do Azure por aplicação e restringir os segredos armazenados num cofre a uma aplicação e a uma equipa de programadores específica.

### <a name="integrate-with-other-azure-services"></a>Integrar com outros serviços do Azure

Enquanto arquivo seguro no Azure, Cofre de chaves foi utilizado para simplificar cenários como:
-  [Azure Disk Encryption](../security/azure-security-disk-encryption.md)
-  O [são sempre encriptados]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) funcionalidade no SQL server e base de dados do Azure SQL
- [Serviço de aplicações do Azure]( https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site). 

O próprio Key Vault pode ser integrado em contas de armazenamento, em hubs de eventos e no Log Analytics.

## <a name="next-steps"></a>Passos Seguintes

- [Quickstart: Criar um Azure Key Vault com a CLI](quick-create-cli.md)
- [Configure an Azure web application to read a secret from Key vault](tutorial-web-application-keyvault.md) (Configurar uma aplicação Web do Azure para ler segredos em cofres de segurança)
