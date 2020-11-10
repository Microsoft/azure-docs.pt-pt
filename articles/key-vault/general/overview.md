---
title: Visão geral do cofre da chave Azure - Cofre da chave Azure
description: A Azure Key Vault é uma loja de segredos seguros, fornecendo gestão de segredos, chaves e certificados, todos apoiados por Módulos de Segurança de Hardware.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.custom: mvc
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 5025c4ad81433b6eb18f5beded860625791ffce5
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445103"
---
# <a name="about-azure-key-vault"></a>Acerca do Azure Key Vault

O Azure Key Vault ajuda a resolver os seguintes problemas:

- **Gestão de Segredos** – O Azure Key Vault pode ser utilizado para armazenar de forma segura e controlar totalmente o acesso aos tokens, palavras-passe, certificados, chaves de API e outros segredos
- **Gestão de Chaves** – O Azure Key Vault também pode ser utilizado como uma solução de Gestão de Chaves. O Azure Key Vault torna mais fácil criar e controlar as chaves de encriptação utilizadas para encriptar os seus dados. 
- **Gestão de Certificados** - Azure Key Vault é também um serviço que permite facilmente a prestação, gestão e implantação de certificados públicos e privados de segurança da camada de transporte/camada de tomadas seguras (TLS/SSL) para utilização com a Azure e os seus recursos internos conectados.

O Azure Key Vault tem dois níveis de serviço: Standard, que encripta com uma chave de software, e um nível Premium, que inclui chaves protegidas por HSM. Para ver uma comparação entre os níveis Standard e Premium, consulte a página de preços do [Cofre da Chave Azure](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="why-use-azure-key-vault"></a>Porquê utilizar o Azure Key Vault?

### <a name="centralize-application-secrets"></a>Centralizar os segredos das aplicações

Ao centralizar o armazenamento dos segredos das aplicações no Azure Key Vault, pode controlar a distribuição dos mesmos. O Key Vault reduz grandemente as possibilidades de haver fugas de segredos acidentais. Se o Key Vault for utilizado, os programadores de aplicações já não precisam de armazenar informações de segurança nas aplicações. Não ter de armazenar informações de segurança em aplicações elimina a necessidade de fazer desta informação parte do código. Por exemplo, uma aplicação poderá ter de se ligar a uma base de dados. Em vez de armazenar a cadeia de ligação no código da aplicação, pode armazená-la de forma segura no Key Vault.

As suas aplicações podem aceder de forma segura às informações de que necessitam utilizando URIs. Estes URIs permitem que as aplicações recuperem versões específicas de um segredo. Não há necessidade de escrever código personalizado para proteger qualquer informação secreta armazenada no Cofre de Chaves.

### <a name="securely-store-secrets-and-keys"></a>Guarde os segredos e chaves em segurança

O acesso a um cofre de chaves requer autenticação e autorização adequadas antes de um “chamador” (utilizador ou aplicação) poder obter acesso. A autenticação estabelece a identidade do chamador, ao passo que a autorização determina as operações que aquele pode fazer.

A autenticação é feita através do Azure Active Directory. A autorização pode ser feita através do controlo de acesso baseado em funções Azure (Azure RBAC) ou da política de acesso do Cofre chave. O Azure RBAC é usado quando se lida com a gestão dos cofres e a política de acesso ao cofre chave é usada para tentar aceder a dados armazenados num cofre.

Os Cofres de Chaves Azure podem estar protegidos por software ou, com o nível Azure Key Vault Premium, protegido por módulos de segurança de hardware (HSMs). Chaves, segredos e certificados protegidos por software são protegidos pela Azure, utilizando algoritmos padrão da indústria e comprimentos de chave.  Para situações em que necessite de uma garantia adicional, pode importar ou gerar chaves em HSMs que nunca saem do limite do HSM. O Azure Key Vault utiliza hSMs nCipher, que são normas federais de processamento de informação (FIPS) 140-2 Nível 2 validados. Pode utilizar ferramentas nCipher para mover uma chave do seu HSM para o Cofre de Chaves Azure.

Por fim, o Azure Key Vault foi concebido para que a Microsoft não veja ou extraia os seus dados.

### <a name="monitor-access-and-use"></a>Monitorizar o acesso e a utilização

Depois de criar alguns Key Vaults, vai querer monitorizar como e quando é que as chaves e segredos são acedidos. Pode monitorizar a atividade, permitindo o registo dos seus cofres. Pode configurar o Azure Key Vault para:

- Arquivar numa conta de armazenamento.
- Transmitir em fluxo para um hub de eventos.
- Envie os registos para os registos do Monitor Azure.

Tem controlo sobre os seus registos, pode protegê-los ao restringir o acesso e também pode eliminar aqueles de que já não precisa.

### <a name="simplified-administration-of-application-secrets"></a>Administração simplificada de segredos de aplicações

Quando armazena dados valiosos, tem de executar vários passos. As informações de segurança devem ser asseguradas, devem seguir um ciclo de vida, e devem estar altamente disponíveis. O Azure Key Vault simplifica o processo de cumprimento destes requisitos:

- Eliminação da necessidade de conhecimento interno dos Módulos de Segurança de Hardware.
- Escalonamento em cima da hora para atender os picos de uso da sua organização.
- Replicar os conteúdos do seu cofre de chaves numa região e para uma região secundária. A replicação de dados garante uma elevada disponibilidade e retira a necessidade de qualquer ação do administrador para desencadear a falha.
- Disponibilizar opções de administração padrão do Azure através do portal, da CLI do Azure e do PowerShell.
- Automatizar determinadas tarefas relativas a certificados que comprar junto de ACs públicas, como inscrição e renovação.

Além disso, o Azure Key Vault permite-lhe segregar segredos das aplicações. As aplicações podem aceder apenas ao cofre a que podem aceder, podendo limitar-se apenas a realizar operações específicas. Pode criar um cofre de chaves do Azure por aplicação e restringir os segredos armazenados num cofre a uma aplicação e a uma equipa de programadores específica.

### <a name="integrate-with-other-azure-services"></a>Integrar com outros serviços do Azure

Como uma loja segura em Azure, Key Vault tem sido usado para simplificar cenários como:
-  [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md)
-  A funcionalidade de [encriptação]( https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-ver15) de dados [sempre encriptada]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) e transparente no servidor SQL e na Base de Dados Azure SQL
- [Serviço de Aplicações Azure]( https://docs.microsoft.com/azure/app-service/configure-ssl-certificate). 

O próprio Key Vault pode ser integrado em Contas de Armazenamento, em Hubs de Eventos e no Log Analytics.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [chaves, segredos e certificados](about-keys-secrets-certificates.md)
- [Início Rápido: Criar um Azure Key Vault com a CLI](../secrets/quick-create-cli.md)
- [Autenticação, pedidos e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Programador do Key Vault](../general/developers-guide.md)
