---
title: Arquitetura OPC Vault - Azure | Microsoft Docs
description: Arquitetura de serviço de gestão de certificados OPC Vault
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: eb558d967ad657d14158684fba92b13979ea5fe2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91281593"
---
# <a name="opc-vault-architecture"></a>Arquitetura do Cofre OPC

> [!IMPORTANT]
> Enquanto atualizamos este artigo, consulte [a Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

Este artigo dá uma visão geral sobre o microservice OPC Vault e o módulo OPC Vault IoT Edge.

Os pedidos da OPC UA utilizam certificados de instância de aplicação para fornecer segurança ao nível da aplicação. Uma ligação segura é estabelecida utilizando criptografia assimétrica, para a qual os certificados de inscrição fornecem o par de chaves público e privado. Os certificados podem ser auto-assinados ou assinados por uma Autoridade de Certificados (CA).

Uma aplicação da UA da OPC tem uma lista de certificados fidedignos que representam as aplicações em que confia. Estes certificados podem ser auto-assinados ou assinados por um CA, ou podem ser um Root-CA ou um sub-CA em si. Se um certificado de confiança fizer parte de uma cadeia de certificados maior, o pedido fideod o certificado que acordam até ao certificado na lista de fidedignidade. Isto é verdade desde que a cadeia completa de certificados possa ser validada.

A grande diferença entre confiar em certificados auto-assinados e confiar num certificado de AC é o esforço de instalação necessário para implantar e manter a confiança. Há também um esforço adicional para hospedar uma AC específica da empresa. 

Para distribuir a confiança para certificados auto-assinados para vários servidores com uma única aplicação de cliente, tem de instalar todos os certificados de aplicação do servidor na lista de fidedignidade da aplicação do cliente. Além disso, deve instalar o certificado de aplicação do cliente em todas as listas de fidedignidades de aplicações do servidor. Este esforço administrativo é um grande fardo, e até aumenta quando se tem de considerar a vida útil dos certificados e renovar os certificados.

O uso de um CA específico da empresa pode simplificar muito a gestão da confiança com vários servidores e clientes. Neste caso, o administrador gera um certificado de instância de aplicação assinado pela AC uma vez para cada cliente e servidor utilizado. Além disso, o Certificado CA está instalado em todas as listas de fidedignidade de aplicações, em todos os servidores e clientes. Com esta abordagem, apenas os certificados caducados precisam de ser renovados e substituídos para os pedidos afetados.

O serviço de gestão de certificados Azure Industrial IoT OPC UA ajuda-o a gerir um CA específico da empresa para aplicações UA OPC. Este serviço baseia-se no microserviço OPC Vault. O OPC Vault fornece um microserviço para hospedar um CA específico da empresa numa nuvem segura. Esta solução é apoiada por serviços assegurados pela Azure Ative Directory (Azure AD), Azure Key Vault com Módulos de Segurança de Hardware (HSMs), Azure Cosmos DB e opcionalmente IoT Hub como loja de aplicações.

O microserviço OPC Vault foi concebido para apoiar o fluxo de trabalho baseado em funções, onde administradores de segurança e aprovadores com direitos de assinatura no Azure Key Vault aprovam ou rejeitam pedidos.

Para compatibilidade com as soluções UA OPC existentes, os serviços incluem suporte para um módulo de borda apoiado por microservice OPC Vault. Isto implementa a interface **OPC UA Global Discovery Server e Certificate Management,** para distribuir certificados e listas de confiança de acordo com a Parte 12 da especificação. 


## <a name="architecture"></a>Arquitetura

A arquitetura baseia-se no microserviço OPC Vault, com um módulo OPC Vault IoT Edge para a rede de fábrica e uma amostra web UX para controlar o fluxo de trabalho:

![Diagrama da arquitetura OPC Vault](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="opc-vault-microservice"></a>Microserviço do Cofre OPC

O microserviço OPC Vault consiste nas seguintes interfaces para implementar o fluxo de trabalho para distribuir e gerir um CA específico da empresa para aplicações OPC UA.

### <a name="application"></a>Aplicação 
- Uma aplicação UA OPC pode ser um servidor ou um cliente, ou ambos. O OPC Vault serve neste caso como uma autoridade de registo de pedidos. 
- Além das operações básicas de registo, atualização e não registo de aplicações, existem também interfaces para encontrar e consultar aplicações com expressões de pesquisa. 
- Os pedidos de certificado devem fazer referência a um pedido válido, a fim de processar um pedido e emitir um certificado assinado com todas as extensões específicas da OPC UA. 
- O serviço de aplicação é apoiado por uma base de dados em Azure Cosmos DB.

### <a name="certificate-group"></a>Grupo de certificados
- Um grupo de certificados é uma entidade que armazena um ca raiz ou um certificado sub-CA, incluindo a chave privada para assinar certificados. 
- O comprimento da chave RSA, o comprimento do hash SHA-2, e as vidas são configuráveis tanto para o Emiter CA como para os certificados de inscrição assinados. 
- Você armazena os certificados de CA no Cofre da Chave Azure, apoiados com FIPS 140-2 Nível 2 HSM. A chave privada nunca sai do armazenamento seguro, porque a assinatura é feita por uma operação Key Vault protegida pela Azure AD. 
- Pode renovar os certificados de AC ao longo do tempo, e fazê-los permanecer em armazenamento seguro devido ao histórico do Cofre de Chaves. 
- A lista de revogação de cada certificado de CA também é armazenada em Key Vault como um segredo. Quando um pedido não é registado, o certificado de pedido é também revogado na Lista de Revogação de Certificados (CRL) por um administrador.
- Pode revogar certificados individuais, bem como certificados em lote.

### <a name="certificate-request"></a>Pedido de certificado
Um pedido de certificado implementa o fluxo de trabalho para gerar um novo par de chaves ou um certificado assinado, utilizando um Pedido de Assinatura de Certificado (CSR) para um pedido de UA OPC. 
- O pedido é armazenado numa base de dados com informações que acompanham, como o sujeito ou uma RSE, e uma referência à aplicação da UA da OPC. 
- A lógica de negócio no serviço valida o pedido contra a informação armazenada na base de dados de aplicações. Por exemplo, a aplicação Uri na base de dados deve coincidir com a aplicação Uri na RSE.
- Um administrador de segurança com direitos de assinatura (isto é, a função approver) aprova ou rejeita o pedido. Se o pedido for aprovado, é gerado um novo par-chave ou certificado assinado (ou ambos). A nova chave privada está guardada de forma segura no Cofre-Chave, e o novo certificado público assinado está armazenado na base de dados de pedidos de certificado.
- O solicitador pode sondar o estado do pedido até que seja aprovado ou revogado. Se o pedido for aprovado, a chave privada e o certificado podem ser descarregados e instalados na loja de certificados da aplicação OPC UA.
- O solicitador pode agora aceitar o pedido de eliminação de informações desnecessárias da base de dados de pedidos. 

Ao longo do tempo de vida de um certificado assinado, um pedido pode ser eliminado ou uma chave pode ficar comprometida. Neste caso, um gestor da AC pode:
- Eliminar uma aplicação, que também elimina todos os pedidos de certificados pendentes e aprovados da aplicação. 
- Elimine apenas um pedido de certificado único, se apenas uma chave for renovada ou comprometida.

Agora os pedidos de certificado aprovados e aceites comprometidos são marcados como eliminados.

Um gestor pode renovar regularmente o Emitente CA CRL. No momento da renovação, todos os pedidos de certificado suprimidos são revogados e os números de série do certificado são adicionados à lista de revogação do CRL. Os pedidos de certificado revogados são marcados como revogados. Em casos urgentes, os pedidos de certificado único também podem ser revogados.

Finalmente, os CRLs atualizados estão disponíveis para distribuição para os clientes e servidores OPC UA participantes.

## <a name="opc-vault-iot-edge-module"></a>Módulo OPC Vault IoT Edge
Para suportar uma rede de fábrica Global Discovery Server, pode implantar o módulo OPC Vault na borda. Executá-lo como uma aplicação local .NET Core, ou iniciá-lo em um recipiente Docker. Note que devido à falta de suporte de autenticação Auth2 na atual stack OPC UA .NET Standard, a funcionalidade do módulo de borda do cofre OPC está limitada a uma função Reader. Um utilizador não pode ser personificado do módulo de borda para o microserviço utilizando a interface padrão OPC UA GDS.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre a arquitetura OPC Vault, pode:

> [!div class="nextstepaction"]
> [Construa e implemente o Cofre OPC](howto-opc-vault-deploy.md)
