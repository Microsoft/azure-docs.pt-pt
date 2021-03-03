---
title: Segurança para atualização de dispositivos para Azure IoT Hub | Microsoft Docs
description: Compreenda como a Atualização do Dispositivo para o IoT Hub garante que os dispositivos são atualizados de forma segura.
author: lichris
ms.author: lichris
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub
ms.openlocfilehash: cf05d5f93180db91658d0e94a23359edd5b0f7ad
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663638"
---
# <a name="device-update-security-model"></a>Modelo de segurança de atualização de dispositivos

A Atualização do Dispositivo para o IoT Hub oferece um método seguro para implementar atualizações para firmware, imagens e aplicações do dispositivo para os seus dispositivos IoT. O fluxo de trabalho fornece um canal seguro de ponta a ponta com um modelo completo de cadeia de custódia que um dispositivo pode usar para provar que uma atualização é confiável, não modificada e intencional.

Cada passo no fluxo de trabalho de Atualização do Dispositivo é protegido através de várias funcionalidades e processos de segurança para garantir que cada passo no oleoduto executa uma entrega segura para o próximo. O cliente de Atualização de Dispositivos identifica e gere corretamente quaisquer pedidos de atualização ilegítimo. O cliente também verifica cada download para garantir que o conteúdo é confiável, não modificado, e é intencional.

## <a name="for-solution-operators"></a>Para operadores de soluções

À medida que os Operadores de Soluções importam atualizações na sua instância de Atualização de Dispositivos, o serviço carrega e verifica os ficheiros binários de atualização para garantir que não foram modificados ou trocados por um utilizador malicioso. Uma vez verificado, o serviço de Atualização do Dispositivo gera um manifesto de [atualização](./update-manifest.md) interna com hashes de ficheiro do manifesto de importação e outros metadados. Este manifesto de atualização é então assinado pelo serviço de Atualização de Dispositivos.

Quando o Operador da Solução solicita a atualização de um dispositivo, é enviada uma mensagem assinada sobre o canal IoT Hub protegido para o dispositivo. A assinatura do pedido é validada pelo agente de Atualização de Dispositivos do dispositivo como autêntica. 

Qualquer download binário resultante é garantido através da validação da assinatura manifesto da atualização. O manifesto de atualização contém os hashes de ficheiro binário, por isso, uma vez que o manifesto é confiável, o agente de Atualização de Dispositivos confia nos hashes e corresponde-os aos binários. Uma vez que o binário de atualização tenha sido descarregado e verificado, este é então entregue ao instalador no dispositivo.

## <a name="for-device-builders"></a>Para construtores de dispositivos

Para garantir que o serviço de atualização do dispositivo se reduz a dispositivos simples e de baixo desempenho, o modelo de segurança utiliza chaves assimétricas cruas e assinaturas cruas. Utilizam formatos baseados em JSON, como JSON Web Tokens & JSON Web Keys.

### <a name="securing-update-content-via-the-update-manifest"></a>Garantir o conteúdo da atualização através do manifesto da atualização

O manifesto de atualização é validado utilizando duas assinaturas. As assinaturas são criadas utilizando uma estrutura constituída por chaves de *assinatura* e chaves *de raiz.*

O Agente de Atualização do Dispositivo incorporou chaves públicas que são utilizadas para todos os dispositivos compatíveis com a atualização do dispositivo. Estas são as chaves das *raízes.* As chaves privadas correspondentes são controladas pela Microsoft.

A Microsoft também gera um par de chaves público/privado que não está incluído no Agente de Atualização de Dispositivos ou armazenado no dispositivo. Esta é a chave da *assinatura.*

Quando uma atualização é importada para a Atualização do Dispositivo para IoT Hub, e o manifesto de atualização é gerado pelo serviço, o serviço assina o manifesto usando a chave de assinatura, e inclui a própria chave de assinatura, que é assinada por uma chave raiz. Quando o manifesto de atualização é enviado para o dispositivo, o Agente de Atualização do Dispositivo recebe os seguintes dados de assinatura:

1. O valor da assinatura em si.
2. O algoritmo usado para gerar #1.
3. A informação chave pública da chave de assinatura utilizada para gerar #1.
4. A assinatura da chave de assinatura pública em #3.
5. A identificação da chave de raiz pública utilizada para gerar #3.
6. O algoritmo usado para gerar #4.

O Agente de Atualização do Dispositivo utiliza as informações acima definidas para validar que a assinatura da chave de assinatura pública é assinada pela tecla raiz. O Agente de Atualização do Dispositivo valida então que a assinatura manifesto de atualização é assinada pela chave de assinatura. Se todas as assinaturas estiverem corretas, o manifesto de atualização é fidedigno pelo Agente de Atualização do Dispositivo. Uma vez que o manifesto de atualização inclui os hashes de ficheiros que correspondem aos próprios ficheiros de atualização, os ficheiros de atualização também podem ser confiáveis se os hashes corresponderem.

Ter chaves de raiz e de assinatura permite à Microsoft rolar periodicamente a chave de assinatura, uma melhor prática de segurança.

### <a name="json-web-signature-jws"></a>Assinatura Web JSON (JWS)

As `updateManifestSignature` informações contidas no sistema `updateManifest` não foram adulteradas. O `updateManifestSignature` é produzido usando uma Assinatura Web JSON com Chaves Web JSON, permitindo a verificação de origem. A assinatura é uma cadeia codificada Base64Url com três secções delineadas por "."  Consulte os métodos de ajuda jws_util.h para analisar e verificar as chaves e fichas JSON.

JSON Web Signature é uma [norma IETF proposta](https://tools.ietf.org/html/rfc7515) amplamente utilizada para a assinatura de conteúdos usando estruturas de dados baseadas em JSON. É uma forma de garantir a integridade dos dados verificando a assinatura dos dados. Mais informações podem ser encontradas no JSON Web Signature (JWS) [RFC 7515](https://www.rfc-editor.org/info/rfc7515).

### <a name="json-web-token"></a>JSON Web Token

JSON Web Tokens é um método [padrão](https://tools.ietf.org/html/rfc7519) aberto da indústria para representar as reclamações de forma segura entre duas partes.

### <a name="root-keys"></a>Chaves de raiz

Cada dispositivo de atualização de dispositivos contém um conjunto de chaves de raiz. Estas chaves são a raiz da confiança para todas as assinaturas da Atualização do Dispositivo. Qualquer assinatura deve ser acorrentada através de uma destas chaves de raiz para ser considerada legítima.

O conjunto de teclas de raiz mudará ao longo do tempo, uma vez que é adequado rodar periodicamente as teclas de assinatura para fins de segurança. Como resultado, o software do agente de atualização de dispositivos terá de se atualizar com o mais recente conjunto de teclas de raiz. 

### <a name="signatures"></a>Assinaturas

Todas as assinaturas serão acomodadas por uma chave de assinatura (pública) assinada por uma das chaves de raiz. A assinatura identificará qual a chave de raiz usada para assinar a chave de assinatura. 

Um agente de Atualização de Dispositivos deve validar as assinaturas, validando primeiro que a assinatura da chave de assinatura (pública) é adequada, válida e assinada por uma das chaves de raiz aprovadas. Uma vez que a chave de assinatura seja validada com sucesso, a assinatura em si pode ser validada utilizando a chave pública de assinatura agora confiável.

As teclas de assinatura são giradas numa cadência muito mais rápida do que as teclas de raiz, por isso espere mensagens assinadas por várias teclas de assinatura diferentes. 

A revogação de uma chave de assinatura é gerida pelo serviço De Atualização de Dispositivos, pelo que os utilizadores não devem tentar cache de chaves de assinatura. Utilize sempre a chave de assinatura que acompanha uma assinatura.

### <a name="receiving-updates"></a>Receber atualizações

Os pedidos de atualização recebidos por um agente de Atualização de Dispositivos conterão um documento assinado do Manifesto de Atualização (UM). O agente deve validar que a assinatura da UM é adequada e intacta. Isto é feito através da validação de que a chave de assinatura da UM foi assinada por uma chave de raiz adequada. Uma vez feito, o agente valida a assinatura UM contra a chave de assinatura.

Uma vez validada a assinatura UM, o agente de Atualização de Dispositivos pode confiar nela como uma "fonte de verdade". Toda a confiança de segurança provém desta fonte. 

A UM contém URLs e ficheiros de conteúdos para descarregar e instalar. Uma vez que o agente tenha descarregado um binário de atualização, deve validar a atualização contra o haxixe de ficheiro encontrado na UM. Isto fornece um modelo transitório de confiança para validação de descarregamento. Não só garante que o conteúdo está intacto (não modificado), como também confirma que o que foi descarregado foi, de facto, o que se pretendia descarregar. 

### <a name="securing-the-device"></a>Segurança do Dispositivo

É importante garantir que os ativos de segurança relacionados com a Atualização do Dispositivo estão devidamente protegidos e protegidos no seu dispositivo. Os ativos, tais como as chaves de raiz, devem ser protegidos contra modificações. Existem várias formas de o fazer, tais como a utilização de dispositivos de segurança (TPM, SGX, HSM, outros dispositivos de segurança) ou até mesmo a sua codificação no agente de Atualização de Dispositivos. Este último requer que o código do agente de atualização do dispositivo seja assinado digitalmente e que o suporte de Integridade do Código do sistema esteja habilitado a proteger contra modificações maliciosas do código do agente.

Podem ser justificadas medidas de segurança adicionais, tais como garantir que a transferência do componente para o componente seja efetuada de forma segura. Por exemplo, registar uma conta isolada específica para executar os vários componentes. E limitar as comunicações baseadas em rede (por exemplo, chamadas DEAPI) apenas para locais.

**[Próximo passo: Saiba mais sobre como a Atualização de Dispositivos utiliza o Azure RBAC](.\device-update-control-access.md)**