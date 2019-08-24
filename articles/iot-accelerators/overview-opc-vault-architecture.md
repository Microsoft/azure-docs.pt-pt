---
title: Arquitetura do cofre OPC – Azure | Microsoft Docs
description: Arquitetura do serviço de gerenciamento de certificados do cofre OPC
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 9331473402ddd22180df3b404824969360d48164
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69995822"
---
# <a name="opc-vault-architecture"></a>Arquitetura do cofre OPC

Este artigo fornece uma visão geral sobre o microserviço do **cofre OPC** e o **módulo de IOT Edge do cofre OPC**.

## <a name="overview"></a>Descrição geral

Os aplicativos OPC UA usam certificados de instância de aplicativo para fornecer segurança em nível de aplicativo. Uma conexão segura é estabelecida usando a criptografia assimétrica, para a qual os certificados de aplicativo fornecem o par de chaves pública e privada. Os certificados podem ser autoassinados ou podem ser assinados por uma autoridade de certificação (CA).

Um aplicativo OPC UA tem uma lista de certificados confiáveis que representam os aplicativos aos quais ele confia. Esses certificados podem ser autoassinados, assinados por uma CA ou podem ser uma CA raiz ou uma subautoridade de certificação em si. Se um certificado confiável fizer parte de uma cadeia de certificados maior, o aplicativo confiará em todos os certificados que se encadeadom ao certificado na lista de confiança, desde que a cadeia de certificados completa possa ser validada.

A principal diferença entre a confiança de certificados autoassinados e a confiança de um certificado de autoridade de certificação é o esforço de instalação necessário para implantar e manter a confiança e o esforço adicional para hospedar uma autoridade de certificação específica da empresa. 

Para distribuir a confiança para certificados autoassinados para n servidores com um único aplicativo cliente, todos os n certificados de aplicativo do servidor precisam ser instalados na lista de confiança do aplicativo cliente e o certificado do aplicativo cliente deve ser instalado em todos os Listas de confiança do aplicativo do servidor. Esse esforço administrativo é muito difícil e aumenta ainda mais quando os tempos de vida de certificados precisam ser considerados e os certificados são renovados.

O uso de uma autoridade de certificação específica da empresa pode simplificar muito o gerenciamento da confiança com vários servidores e clientes. Nesse caso, o administrador gera um certificado de instância de aplicativo assinado por AC uma vez para cada cliente e servidor usado. Além disso, o certificado de autoridade de certificação é instalado em todas as listas de confiança de aplicativos, em todos os servidores e clientes. Com essa abordagem, somente os certificados expirados precisam ser renovados e substituídos pelos aplicativos afetados.

O serviço de gerenciamento de certificados do IoT UA do Azure industrial do OPC é nossa solução para gerenciar uma AC específica da empresa para aplicativos OPC UA que se baseiam no microserviço do cofre OPC.

O cofre OPC fornece um microserviço para hospedar uma AC específica da empresa em uma nuvem segura, apoiada pelos serviços protegidos do Azure AD com o Azure Key Vault com módulos de segurança de hardware, Cosmos DB e, opcionalmente, o Hub IoT como repositório de aplicativos.

O microserviço do cofre OPC foi projetado para dar suporte ao fluxo de trabalho baseado em função, onde estão as solicitações pessoais, certificados de aplicativo assinados e em que administradores de segurança e aprovadores com direitos de assinatura no Azure Key Vault aprovar ou rejeitar essas solicitações.

Para compatibilidade com as soluções existentes do OPC UA GDS, os serviços incluem suporte para um módulo de borda com backup em microserviço do compartimento OPC, que implementa o *servidor de descoberta global do OPC UA e* a interface de gerenciamento de certificados para distribuir certificados e listas de confiança de acordo com a parte 12 da especificação. No entanto, a partir de nosso conhecimento, essa interface do GDS Server ainda não é amplamente usada e ainda tem funcionalidade limitada (função de leitor). [Sob demanda, melhoraremos a experiência na solicitação do cliente (*)](#yet-unsupported-features).

## <a name="architecture"></a>Arquitetura

A arquitetura é baseada no microserviço do cofre OPC com um cofre do OPC IoT Edge módulo para a rede de fábrica e um exemplo de UX da Web para controlar o fluxo de trabalho:

![Arquitetura do OPCVault](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="opc-vault-microservice"></a>Microserviço do cofre OPC

O microserviço do cofre OPC consiste nas seguintes interfaces para implementar o fluxo de trabalho para distribuir e gerenciar uma AC específica da empresa para aplicativos OPC UA:

### <a name="application"></a>Aplicação 
- Um "aplicativo OPC UA" pode ser um servidor ou um cliente ou ambos. O cofre OPC serve, nesse caso, como uma autoridade de registro de aplicativo. 
- Ao lado das operações básicas para registrar, atualizar e cancelar o registro de aplicativos, também há interfaces para localizar e consultar aplicativos com expressões de pesquisa. 
- As solicitações de certificado precisam fazer referência a um aplicativo válido para processar uma solicitação e emitir um certificado assinado com todas as extensões específicas do OPC UA. 
- O serviço de aplicativo é apoiado por um banco de dados CosmosDB ou pelo [registro do dispositivo OpcTwin (*)](#yet-unsupported-features), dependendo da configuração do cliente.

### <a name="certificate-group"></a>Grupo de certificados
- Um grupo de certificados é uma entidade, que armazena uma autoridade de certificação raiz ou um certificado de subautoridade de certificação, incluindo a chave privada para assinar certificados. 
- O comprimento de chave RSA, o comprimento do hash SHA-2 e os tempos de vida são configuráveis para a AC do emissor e certificados de aplicativo assinados. 
- Vários grupos podem ser hospedados em um único serviço para permitir extensões futuras com grupos de certificados HTTPS, de usuário ou de algoritmo ECC [(*)](#yet-unsupported-features). 
- Os certificados de autoridade de certificação são armazenados em Azure Key Vault com suporte para os módulos de segurança de hardware (HSM) do FIPS 140-2 nível 2. A chave privada nunca deixa o armazenamento seguro porque a assinatura é feita por uma operação de Key Vault protegida AzureAD. 
- Os certificados de autoridade de certificação podem ser renovados ao longo do tempo e ainda permanecem no armazenamento seguro devido ao histórico de Key Vault. 
- A lista de revogação de cada certificado de autoridade de certificação também é armazenada em Key Vault como um segredo. Depois que um aplicativo tem o registro cancelado, o certificado do aplicativo também é revogado na CRL por um administrador.
- Há suporte para a revogação de certificado único e em lote.

### <a name="certificate-request"></a>Solicitação de certificado
Uma solicitação de certificado implementa o fluxo de trabalho para gerar um novo par de chaves ou um certificado assinado usando uma "solicitação de assinatura de certificado" (CSR) para um aplicativo OPC UA. 
- A solicitação é armazenada em um banco de dados com informações complementares, como o assunto ou um CSR e uma referência ao aplicativo OPC UA. 
- A lógica de negócios no serviço valida a solicitação em relação às informações armazenadas no banco de dados do aplicativo. Por exemplo, o URI do aplicativo no banco de dados deve corresponder ao URI do aplicativo no CSR.
- Um administrador de segurança com direitos de assinatura (função de aprovador) aprova ou rejeita a solicitação. Se a solicitação for aprovada, um novo par de chaves e/ou um certificado assinado serão gerados. A nova chave privada é armazenada com segurança no keyvault enquanto o novo certificado público assinado é armazenado no banco de dados de solicitação de certificado.
- O solicitante pode sondar o status da solicitação até que seja aprovado ou revogado. Se a solicitação foi aprovada, a chave privada e o certificado podem ser baixados e instalados no repositório de certificados do aplicativo OPC UA.
- O solicitante agora pode aceitar a solicitação para excluir informações desnecessárias do banco de dados de solicitação. 

Durante o tempo de vida de um certificado assinado, um aplicativo pode ser excluído ou uma chave pode ficar comprometida. Nesse caso, um Gerenciador de CA pode:
- Exclua um aplicativo, que, ao mesmo tempo, exclui também todas as solicitações de certificado pendentes e aprovadas do aplicativo. 
- Outra opção é excluir apenas uma única solicitação de certificado se apenas uma chave for renovada ou comprometida.
- Agora, as solicitações de certificado aprovadas e aceitas são marcadas como excluídas.
- Um gerente pode executar regularmente uma renovação da CRL da autoridade de certificação do emissor. No momento da renovação, todas as solicitações de certificado excluídas são revogadas e os números de série do certificado são adicionados à lista de revogação de CRL. As solicitações de certificado revogadas são marcadas como revogadas.
- Em eventos urgentes, as solicitações de certificado único também podem ser revogadas.
- Por fim, as CRLs atualizadas estão disponíveis para distribuição para os clientes e servidores do OPC UA participantes.

Para obter mais informações sobre a API de microserviço do compartimento OPC, consulte a documentação do Swagger do Microservice.

## <a name="opc-vault-iot-edge-module"></a>Módulo de IoT Edge do cofre OPC
Para dar suporte a um servidor de descoberta global de rede de fábrica, o módulo do cofre do OPC pode ser implantado na borda, executar como um aplicativo de .Net Core local ou pode ser iniciado em um contêiner do Docker. Devido à falta de suporte à autenticação Auth2 na pilha padrão atual do OPC UA .net, a funcionalidade do módulo de borda do OPC Vault é limitada a uma função de leitor, pois um usuário não pode ser representado do módulo de borda para o micro Service usando o padrão OPC UA GDS  interface. Somente as operações, que não exigem o gravador, o administrador ou a função de aprovador, são permitidas neste ponto[(*)](#yet-unsupported-features). 

## <a name="yet-unsupported-features"></a>Recursos do ainda sem suporte

**(*)** ainda não tem suporte.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu sobre a arquitetura do cofre do OPC, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Compilar e implantar o cofre OPC](howto-opc-vault-deploy.md)
