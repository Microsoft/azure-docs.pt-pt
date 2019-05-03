---
title: Segurança do Azure do serviço de Blockchain
description: Conceitos do Azure Blockchain serviço dados acesso e segurança
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: dd0a33364ed9395a85478798e47352c533bd47dc
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028204"
---
# <a name="azure-blockchain-service-security"></a>Segurança do Azure do serviço de Blockchain

O serviço do Azure Blockchain utiliza várias capacidades do Azure para manter os seus dados seguros e disponíveis. Dados são protegidos através do isolamento, encriptação e autenticação.

## <a name="isolation"></a>Isolamento

Recursos do Azure do serviço de Blockchain estejam isolados numa rede virtual privada. Cada nó de transação e a validação é uma máquina virtual (VM). As VMs numa rede virtual não podem comunicar diretamente para as VMs numa rede virtual diferente. Isolamento assegura a comunicação são mantidos privada dentro da rede virtual. Para obter mais informações sobre o isolamento de rede virtual do Azure, consulte [isolamento na Cloud pública do Azure](../../security/azure-isolation.md#networking-isolation).

![Diagrama VNET](./media/data-security/vnet.png)

## <a name="encryption"></a>Encriptação

Dados de utilizador são armazenados no armazenamento do Azure. Dados de utilizador são encriptados em trânsito e em inatividade para segurança e confidencialidade. Para obter mais informações, consulte: [Guia de segurança de armazenamento do Azure](../../storage/common/storage-security-guide.md).

## <a name="authentication"></a>Autenticação

Transações podem ser enviadas para nós de blockchain por meio de um ponto de extremidade RPC. Os clientes comunicam com um nó de transação a utilizar um servidor de proxy inverso que a autenticação de utilizador identificadores e encripta os dados através de SSL.

![Diagrama de autenticação](./media/data-security/authentication.png)

Existem três modos de autenticação para acesso RPC.

### <a name="basic-authentication"></a>Autenticação básica

Autenticação básica utiliza um cabeçalho de autenticação HTTP que contém o nome de utilizador e palavra-passe. Nome de utilizador é o nome do nó de blockchain. Palavra-passe é definida durante o aprovisionamento de um membro ou um nó. A palavra-passe pode ser alterada com o portal do Azure ou a CLI.

### <a name="access-keys"></a>Chaves de acesso

Chaves de acesso ao utilizam uma cadeia de caracteres gerada aleatoriamente incluída no URL do ponto final. Duas chaves de acesso ajudam a habilitar a rotação de chaves. As chaves podem ser regeneradas do portal do Azure e CLI.

### <a name="azure-active-directory"></a>Azure Active Directory

O Azure Active Directory (Azure AD) utiliza uma afirmação com base no mecanismo de autenticação em que o utilizador é autenticado pelo Azure AD com credenciais de utilizador do Azure AD. Azure AD fornece gestão de identidade com base na cloud e permite aos clientes utilizar uma única identidade entre um aplicativos inteiros de enterprise e o acesso na cloud. O serviço do Azure Blockchain integra-se com o Azure AD para ativar a Federação de ID, é necessário um início de sessão e multi-factor authentication de único. Pode atribuir utilizadores, grupos e funções de aplicação na sua organização para o acesso de membro e o nó de blockchain.

O proxy de cliente do Azure AD está disponível no [GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases). O proxy de cliente direciona o utilizador para a página de início de sessão do Azure AD e obtém um token de portador quando a autenticação com êxito. Posteriormente, o utilizador liga-se uma aplicação de cliente Ethereum como Geth ou Truffle ao ponto de extremidade do proxy de cliente. Por fim, quando é submetida uma transação, o proxy do cliente injeta o token de portador no cabeçalho de http e o proxy inverso valida o token através do protocolo de OAuth.

## <a name="keys-and-ethereum-accounts"></a>As chaves e Ethereum contas

Quando é gerado um membro de serviço do Azure Blockchain, Ethereum uma conta de serviço e um par de chaves público e privado de aprovisionamento. A chave privada é utilizada para enviar transações para o blockchain. A conta de Ethereum é os último 20 bytes de hash da chave pública. A conta de Ethereum também é denominada uma carteira.

O par de chaves público e privado é armazenado como um ficheiro de chave no formato JSON. A chave privada é encriptada com a palavra-passe que introduziu quando é criado pelo serviço de contabilidade de blockchain.

As chaves privadas são usadas para assinar digitalmente as transações. Os blockchains privados, um contrato inteligente assinado por uma chave privada representa identidade o assinante. Para verificar a validade da assinatura, o recetor pode comparar a chave pública do assinante com o endereço calculado da assinatura.

Chaves de constellation são utilizadas para identificar exclusivamente um nó de quórum. Chaves da constellation são geradas no momento do provisionamento de nó e são especificadas no parâmetro privateFor de uma transação privada de quórum.

## <a name="next-steps"></a>Passos Seguintes

[Configurar os nós de transação de serviço do Azure Blockchain](configure-transaction-nodes.md)
