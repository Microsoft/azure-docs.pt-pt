---
title: Vista geral do cofre da chave azure - Cofre chave Azure / Microsoft Docs
description: O Azure Key Vault é um serviço cloud que funciona como um arquivo de segredos seguro.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.custom: mvc
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 4e2953b107b017d032e737e2878472166c677839
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "78194959"
---
# <a name="what-is-azure-key-vault"></a>O que é o cofre de chave do Azure?

O Cofre chave Azure ajuda a resolver os seguintes problemas:

- **Gestão de Segredos** – O Azure Key Vault pode ser utilizado para armazenar de forma segura e controlar totalmente o acesso aos tokens, palavras-passe, certificados, chaves de API e outros segredos
- **Gestão de Chaves** – O Azure Key Vault também pode ser utilizado como uma solução de Gestão de Chaves. O Azure Key Vault torna mais fácil criar e controlar as chaves de encriptação utilizadas para encriptar os seus dados. 
- **Gestão de Certificados** - Azure Key Vault é também um serviço que lhe permite fornecer, gerir e implementar facilmente certificados de segurança/camada de tomadas seguras de transporte (TLS/SSL) para uso com o Azure e os seus recursos internos conectados. 
- **Guarde segredos apoiados por Módulos** de Segurança de Hardware - Os segredos e chaves podem ser protegidos quer por software quer por HSMs validados de nível 2

## <a name="why-use-azure-key-vault"></a>Porquê utilizar o Azure Key Vault?

### <a name="centralize-application-secrets"></a>Centralizar os segredos das aplicações

Ao centralizar o armazenamento dos segredos das aplicações no Azure Key Vault, pode controlar a distribuição dos mesmos. O Key Vault reduz grandemente as possibilidades de haver fugas de segredos acidentais. Se o Key Vault for utilizado, os programadores de aplicações já não precisam de armazenar informações de segurança nas aplicações. Não ter de armazenar informações de segurança em aplicações elimina a necessidade de fazer desta informação parte do código. Por exemplo, uma aplicação poderá ter de se ligar a uma base de dados. Em vez de armazenar a cadeia de ligação no código da aplicação, pode armazená-la de forma segura no Key Vault.

As suas aplicações podem aceder de forma segura à informação de que necessitam utilizando URIs. Estes URIs permitem que as aplicações recuperem versões específicas de um segredo. Não há necessidade de escrever código personalizado para proteger qualquer informação secreta armazenada no Cofre Chave.

### <a name="securely-store-secrets-and-keys"></a>Guarde os segredos e chaves em segurança

Os segredos e as chaves são salvaguardadas pelo Azure, utilizando os algoritmos de norma da indústria, os comprimentos de chave e os módulos de segurança de hardware (HSMs). Os HSMs têm validação da norma federal norte-americana Federal Information Processing Standards (FIPS) 140-2 Nível 2.

O acesso a um cofre de chaves requer autenticação e autorização adequadas antes de um “chamador” (utilizador ou aplicação) poder obter acesso. A autenticação estabelece a identidade do chamador, ao passo que a autorização determina as operações que aquele pode fazer.

A autenticação é feita através do Azure Active Directory. A autorização pode ser feita através do controlo de acesso baseado em funções (RBAC) ou de uma política de acesso do Key Vault. O RBAC é utilizado para lidar com a gestão dos cofres e a política de acesso do Key Vault é utilizada para o acesso aos dados armazenados nos cofres.

O Azure Key Vaults pode ser protegido por HSMs de software ou hardware. Em casos em que seja preciso mais segurança, pode importar ou gerar chaves nos módulos de segurança de hardware (HSMs) que nunca deixam o limite do HSM. A Microsoft utiliza módulos de segurança de hardware nCipher. Pode utilizar ferramentas nCipher para mover uma chave do seu HSM para o Cofre de Chaves Azure.

Por fim, o Azure Key Vault foi concebido para que a Microsoft não veja ou extraia os seus dados.

### <a name="monitor-access-and-use"></a>Monitorizar o acesso e a utilização

Depois de criar alguns Key Vaults, vai querer monitorizar como e quando é que as chaves e segredos são acedidos. Pode monitorizar a atividade permitindo a exploração madeireira para os seus cofres. Pode configurar o Azure Key Vault para:

- Arquivar numa conta de armazenamento.
- Transmitir em fluxo para um hub de eventos.
- Envie os registos para os registos do Monitor Azure.

Tem controlo sobre os seus registos, pode protegê-los ao restringir o acesso e também pode eliminar aqueles de que já não precisa.

### <a name="simplified-administration-of-application-secrets"></a>Administração simplificada de segredos de aplicações

Quando armazena dados valiosos, tem de executar vários passos. As informações de segurança devem ser protegidas, devem seguir um ciclo de vida e devem estar altamente disponíveis. O Cofre chave Azure simplifica o processo de cumprimento destes requisitos:

- Eliminando a necessidade de conhecimento interno dos Módulos de Segurança de Hardware.
- Aumentar verticalmente com pouca antecedência, para satisfazer os picos de utilização da sua organização.
- Replicar os conteúdos do seu cofre de chaves numa região e para uma região secundária. A replicação de dados garante uma elevada disponibilidade e retira a necessidade de qualquer ação do administrador para desencadear a falha.
- Disponibilizar opções de administração padrão do Azure através do portal, da CLI do Azure e do PowerShell.
- Automatizar determinadas tarefas relativas a certificados que comprar junto de ACs públicas, como inscrição e renovação.

Além disso, o Azure Key Vault permite-lhe segregar segredos das aplicações. As aplicações só podem aceder ao cofre a que podem aceder, e podem limitar-se apenas a executar operações específicas. Pode criar um cofre de chaves do Azure por aplicação e restringir os segredos armazenados num cofre a uma aplicação e a uma equipa de programadores específica.

### <a name="integrate-with-other-azure-services"></a>Integrar com outros serviços do Azure

Como uma loja segura em Azure, key vault tem sido usado para simplificar cenários como:
-  [Azure Disk Encryption](../security/fundamentals/encryption-overview.md)
-  A funcionalidade [sempre encriptada]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) no servidor SQL e na Base de Dados Azure SQL
- [Serviço de Aplicações Azure.]( https://docs.microsoft.com/azure/app-service/configure-ssl-certificate) 

O próprio Key Vault pode ser integrado em Contas de Armazenamento, em Hubs de Eventos e no Log Analytics.

## <a name="next-steps"></a>Passos seguintes

- [Início Rápido: Criar um Azure Key Vault com a CLI](quick-create-cli.md)
- [Configure uma aplicação web Azure para ler um segredo do cofre key](tutorial-web-application-keyvault.md)
