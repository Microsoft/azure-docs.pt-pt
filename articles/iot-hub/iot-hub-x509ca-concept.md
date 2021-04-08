---
title: Conceitos de Azure IoT Hub X.509 | de segurança Microsoft Docs
description: Conceito - compreender o valor dos certificados X.509 da autoridade de certificados no fabrico de dispositivos IoT e autenticação.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 877200cbafbe68fa6161025572abfddad651e172
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96490725"
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>Compreensão conceptual dos certificados X.509 CA na indústria IoT

Este artigo descreve o valor da utilização de certificados X.509 da Autoridade de Certificados (CA) no fabrico e autenticação de dispositivos IoT para o IoT Hub. Inclui informações sobre a configuração da cadeia de fornecimento e vantagens de destaque.

Este artigo descreve:

* Quais são os certificados X.509 CA e como obtê-los

* Como registar o seu certificado X.509 CA no IoT Hub

* Como criar uma cadeia de fornecimento de fabrico para a autenticação baseada em CA X.509

* Como os dispositivos assinados com X.509 CA conectam-se ao IoT Hub

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="overview"></a>Descrição Geral

A autenticação da X.509 Certificate Authority (CA) é uma abordagem para autenticar dispositivos para o IoT Hub utilizando um método que simplifica drasticamente a criação de identidade do dispositivo e a gestão do ciclo de vida na cadeia de fornecimento.

Um atributo diferenciante da autenticação X.509 CA é uma relação de um para muitos que um certificado de CA tem com os seus dispositivos a jusante. Esta relação permite o registo de qualquer número de dispositivos no IoT Hub, registando uma vez um certificado X.509 CA, caso contrário, os certificados exclusivos do dispositivo devem ser pré-registados para cada dispositivo antes de um dispositivo poder ligar-se. Esta relação de um para muitos também simplifica as operações de gestão do ciclo de vida dos certificados de dispositivos.

Outro atributo importante da autenticação X.509 CA é a simplificação da logística da cadeia de abastecimento. A autenticação segura dos dispositivos requer que cada dispositivo detenha um segredo único como uma chave como base para a confiança. Na autenticação baseada em certificados, este segredo é uma chave privada. Um fluxo típico de fabrico de dispositivos envolve vários passos e guardiões. Gerir seguramente as chaves privadas do dispositivo em vários guardiões e manter a confiança é difícil e dispendioso. A utilização de certificados as autoridades resolvem este problema, inscrevendo cada guardião numa cadeia criptográfica de confiança em vez de lhes confiarem chaves privadas do dispositivo. Cada guardião, por sua vez, sinaliza dispositivos na respetiva fase de processo do fluxo de fabrico. O resultado geral é uma cadeia de fornecimento ideal com responsabilidade incorporada através da utilização da cadeia criptográfica de confiança. Vale a pena notar que este processo é o que mais segurança gera quando os dispositivos protegem as suas chaves privadas únicas. Para tal, exortamos à utilização de Módulos Secure de Hardware (HSM) capazes de gerar chaves privadas internamente que nunca verão a luz do dia.

Este artigo oferece uma visão de ponta a ponta da utilização da autenticação X.509 CA, desde a configuração da cadeia de fornecimento até à ligação do dispositivo, ao mesmo tempo que utiliza um exemplo do mundo real para solidificar a compreensão.

Também pode utilizar grupos de inscrição com o Serviço de Provisionamento de Dispositivos Azure IoT Hub (DPS) para lidar com o fornecimento de dispositivos aos hubs. Para obter mais informações sobre a utilização de DPS para a disponibilização de dispositivos de certificado X.509, consulte [Tutorial: Forneça vários dispositivos X.509 utilizando grupos de inscrição](../iot-dps/tutorial-custom-hsm-enrollment-group-x509.md).

## <a name="introduction"></a>Introdução

O certificado X.509 CA é um certificado digital cujo titular pode assinar outros certificados. Este certificado digital é X.509 porque está em conformidade com uma norma de formatação de certificado prescrita pela norma RFC 5280 da IETF, e é uma autoridade de certificados (CA) porque o seu titular pode assinar outros certificados.

A utilização de X.509 CA é melhor compreendida em relação a um exemplo concreto. Considere a Empresa-X, um fabricante de Smart-X-Widgets projetado para instalação profissional. A Empresa-X subcontrata tanto o fabrico como a instalação. Contrata o fabricante Factory-Y para fabricar os Smart-X-Widgets e o prestador de serviços Técnico-Z para instalar. A Empresa-X deseja que o Smart-X-Widget navege diretamente da Factory-Y para o Técnico-Z para instalação e que se ligue diretamente à instância da Empresa-X de IoT Hub após a instalação sem mais intervenções da Empresa-X. Para que isso aconteça, a Empresa-X precisa de completar algumas operações de configuração únicas para primer Smart-X-Widget para ligação automática. Com o cenário de ponta a ponta em mente, o resto deste artigo é estruturado da seguinte forma:

* Adquirir o certificado X.509 CA

* Registre o certificado de CA X.509 para ioT Hub

* Assine os dispositivos numa cadeia de certificados de confiança

* Ligação do dispositivo

## <a name="acquire-the-x509-ca-certificate"></a>Adquirir o certificado X.509 CA

A Empresa-X tem a opção de adquirir um certificado X.509 CA a uma autoridade de certificados de raiz pública ou criar um através de um processo auto-assinado. Uma opção seria ideal em relação à outra, dependendo do cenário de aplicação. Independentemente da opção, o processo implica dois passos fundamentais, gerando um par chave público/privado e assinando a chave pública num certificado.

![Fluxo para gerar certificados X509CA](./media/iot-hub-x509ca-concept/csr-flow.png)

Os detalhes sobre como realizar estes passos diferem com vários prestadores de serviços.

### <a name="purchasing-an-x509-ca-certificate"></a>Compra de um certificado X.509 CA

A aquisição de um certificado de CA tem o benefício de ter uma ca de raiz bem conhecida como um terceiro de confiança para garantir a legitimidade dos dispositivos IoT quando os dispositivos se ligam. A Empresa-X escolheria esta opção se pretendesse que o Smart-X-Widget interagisse com produtos ou serviços de terceiros após a ligação inicial ao IoT Hub.

Para adquirir um certificado X.509 CA, a Empresa-X escolheria um prestador de serviços de certificados de raiz. Uma pesquisa na Internet para a frase "Root CA" produzirá boas pistas. A CA de raiz irá orientar a Empresa-X sobre como criar o par de chaves público/privado e como gerar um Pedido de Assinatura de Certificado (CSR) para os seus serviços. Uma RSE é o processo formal de pedido de certificado a uma autoridade de certificados. O resultado desta compra é um certificado para uso como certificado de autoridade. Dada a ubiquidade dos certificados X.509, é provável que o certificado tenha sido devidamente formatado para a norma RFC 5280 do IETF.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Criação de um certificado ca Self-Signed X.509

O processo de criação de um certificado de CA Self-Signed X.509 é semelhante à compra, com exceção de envolver um signatário de terceiros como a autoridade do certificado raiz. No nosso exemplo, a Empresa-X assinará o seu certificado de autoridade em vez de uma autoridade de certificados de raiz. A Empresa-X pode escolher esta opção para testes até estarem prontos para comprar um certificado de autoridade. A Empresa-X também pode utilizar um certificado X.509 CA auto-assinado em produção, se o Smart-X-Widget não se destinar a ligar a quaisquer serviços de terceiros fora do IoT Hub.

## <a name="register-the-x509-certificate-to-iot-hub"></a>Registe o certificado X.509 no IoT Hub

A Empresa-X precisa de registar o X.509 CA para ioT Hub, onde servirá para autenticar Smart-X-Widgets à medida que se ligam. Este é um processo único que permite autenticar e gerir qualquer número de dispositivos Smart-X-Widget. Trata-se de um processo único devido a uma relação entre certificados de CA e certificados de dispositivo assinados pelo certificado de AC ou por um certificado intermédio. Esta relação constitui uma das principais vantagens da utilização do método de autenticação X.509 CA. A alternativa é carregar as impressões digitais individuais do certificado para cada dispositivo Smart-X-Widget, aumentando assim os custos operacionais.

Registar o certificado X.509 CA é um processo em duas etapas, o upload do certificado e o comprovativo de posse de certificado.

![Registo de um certificado X509CA](./media/iot-hub-x509ca-concept/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>Upload do Certificado X.509 CA

O processo de upload do certificado X.509 CA é apenas isso, faça o upload do certificado de CA para IoT Hub. O IoT Hub espera o certificado num ficheiro. A Empresa-X simplesmente carrega o ficheiro de certificado. O ficheiro de certificado NÃO DEVE, em caso algum, conter chaves privadas. As melhores práticas das normas que regem a Infraestrutura De Chaves Públicas (PKI) determinam que o conhecimento do privado da Empresa-X neste caso reside exclusivamente na Empresa-X.

### <a name="proof-of-possession-of-the-certificate"></a>Prova de posse do certificado

O certificado X.509 CA, tal como qualquer certificado digital, é informação pública suscetível a escutas. Como tal, um espectador pode intercetar um certificado e tentar carregá-lo como seu. No nosso exemplo, o IoT Hub gostaria de ter a certeza de que o certificado ca Company-X está a carregar realmente pertence à Empresa-X. Fá-lo desafiando a Empresa-X a provar que, de facto, possuem o certificado através de um [fluxo de prova de posse (PoP).](https://tools.ietf.org/html/rfc5280#section-3.1) O fluxo de prova de posse implica que o IoT Hub gera um número aleatório a ser assinado pela Empresa-X utilizando a sua chave privada. Se a Empresa-X seguisse as melhores práticas do PKI e protegesse a sua chave privada, então só estariam em posição de responder corretamente ao desafio da prova de posse. O IoT Hub procede ao registo do certificado X.509 CA após uma resposta bem sucedida do desafio de prova de posse.

Uma resposta bem sucedida ao desafio de prova de posse do IoT Hub completa o registo X.509 CA.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>Assine dispositivos numa cadeia de certificados de confiança

A IoT requer que todos os dispositivos possuam uma identidade única. Estas identidades constam dos certificados de formulário para os regimes de autenticação baseados em certificados. No nosso exemplo, isto significa que todos os Smart-X-Widget devem possuir um certificado de dispositivo único. Como é que a Empresa-X se instala para isto na sua cadeia de fornecimento?

Uma maneira de fazer isto é pré-gerar certificados para Smart-X-Widgets e confiar conhecimentos de chaves privadas de dispositivos únicos correspondentes com parceiros da cadeia de fornecimento. Para a Empresa-X, isto significa confiar a Factory-Y e o Técnico-Z. Embora este seja um método válido, vem com desafios que devem ser ultrapassados para garantir a confiança da seguinte forma:

1. Ter de partilhar chaves privadas de dispositivos com parceiros da cadeia de fornecimento, além de ignorar as melhores práticas do PKI de nunca partilhar chaves privadas, torna a confiança na construção na cadeia de abastecimento cara. Significa que sistemas de capital como salas seguras para alojar chaves privadas de dispositivos, e processos como auditorias de segurança periódicas precisam de ser instalados. Ambos acrescentam custos à cadeia de abastecimento.

2. Contabilizar de forma segura os dispositivos na cadeia de fornecimento e, posteriormente, geri-los em implementação torna-se uma tarefa de um para um para cada par chave-a-dispositivo desde o ponto de certificação única do dispositivo (portanto, chave privada) geração para a reforma do dispositivo. Isto impede a gestão de grupos de dispositivos, a menos que o conceito de grupos seja explicitamente integrado no processo de alguma forma. A gestão segura da contabilidade e do ciclo de vida do dispositivo torna-se, portanto, um pesado fardo de operações. No nosso exemplo, a Companhia X suportaria este fardo.

A autenticação de certificados X.509 CA oferece soluções elegantes para desafios acima listados através da utilização de cadeias de certificados. Uma cadeia de certificados resulta de uma ac que assina uma AC intermédia que, por sua vez, assina outro CA intermédio e assim continua até que um CA intermédio final assine um dispositivo. No nosso exemplo, a Company-X assina o Factory-Y, que por sua vez assina o Técnico-Z que finalmente assina o Smart-X-Widget.

![Hierarquia da cadeia de certificados](./media/iot-hub-x509ca-concept/cert-chain-hierarchy.png)

Acima da cascata de certificados na cadeia apresenta a entrega lógica da autoridade. Muitas cadeias de abastecimento seguem esta entrega lógica em que cada CA intermédio é assinado na cadeia enquanto recebe todos os certificados de CA a montante, e a última AC intermédia finalmente assina cada dispositivo e injeta todos os certificados de autoridade da cadeia no dispositivo. Isto é comum quando a empresa de fabrico de contratos com uma hierarquia de fábricas encomenda uma determinada fábrica para fazer o fabrico. Embora a hierarquia possa ter vários níveis de profundidade (por exemplo, por geografia/tipo de produto/linha de fabrico), apenas a fábrica no final pode interagir com o dispositivo, mas a corrente é mantida a partir do topo da hierarquia.

As cadeias alternativas podem ter diferentes CA interativas interagem com o dispositivo, caso em que a AC interage com o dispositivo injeta o conteúdo da cadeia de certificados nessa altura. Os modelos híbridos também são possíveis onde apenas alguns dos CA têm interação física com o dispositivo.

No nosso exemplo, tanto a Factory-Y como o Técnico-Z interagem com o Smart-X-Widget. Embora a Empresa-X detenha o Smart-X-Widget, na verdade não interage fisicamente com ele em toda a cadeia de fornecimento. A cadeia de certificados de confiança para o Smart-X-Widget compreende, portanto, a Assinatura da Empresa-X Factory-Y que, por sua vez, assina o Técnico-Z que fornecerá então a assinatura final ao Smart-X-Widget. O fabrico e instalação do Smart-X-Widget compreende a Factory-Y e o Técnico-Z utilizando os respetivos certificados de CA intermédios para assinar todos e cada Smart-X-Widgets. O resultado final de todo este processo é o Smart-X-Widgets com certificados de dispositivo únicos e cadeia de certificados de confiança que vai até ao certificado da Company-X CA.

![Cadeia de confiança dos certificados de uma empresa para os certificados de outra empresa](./media/iot-hub-x509ca-concept/cert-mfr-chain.png)

Este é um bom ponto para rever o valor do método X.509 CA. Em vez de pré-gerar e entregar certificados para cada Smart-X-Widget na cadeia de fornecimento, a Empresa-X só teve de assinar a Factory-Y uma vez. Em vez de ter de rastrear todos os dispositivos ao longo do ciclo de vida do dispositivo, a Empresa-X pode agora rastrear e gerir dispositivos através de grupos que naturalmente emergem do processo da cadeia de fornecimento, por exemplo, dispositivos instalados pelo Técnico-Z após julho de algum ano.

Por último, mas não menos importante, o método de autenticação da AC infunde a responsabilidade segura na cadeia de fornecimento de fabrico do dispositivo. Devido ao processo da cadeia de certificados, as ações de cada membro da cadeia são criptograficamente registadas e verificáveis.

Este processo baseia-se em certos pressupostos que devem ser surgidos para a completude. Requer uma criação independente de um único par de chaves público/privado do dispositivo e que a chave privada seja protegida dentro do dispositivo. Felizmente, existem chips de silicone seguros sob a forma de Hardware Secure Modules (HSM) capazes de gerar chaves internamente e proteger chaves privadas. A Empresa-X só precisa de adicionar um desses chips à conta de materiais componentes do Smart-X-Widget.

## <a name="device-connection"></a>Ligação do dispositivo

As secções anteriores acima foram acumulando-se para a ligação do dispositivo. Ao simplesmente registar um certificado de CA X.509 no IoT Hub uma vez, como é que milhões de dispositivos potencialmente se conectam e são autenticados desde a primeira vez?  Simples; através do mesmo upload de certificado e fluxo de prova de posse que encontramos anteriormente com o registo do certificado X.509 CA.

Os dispositivos fabricados para a autenticação X.509 CA estão equipados com certificados exclusivos do dispositivo e uma cadeia de certificados da respetiva cadeia de fornecimento de fabrico. A ligação do dispositivo, mesmo pela primeira vez, acontece num processo em duas etapas: carregamento em cadeia de certificados e prova de posse.

Durante o upload da cadeia de certificados, o dispositivo envia o seu certificado exclusivo do dispositivo juntamente com a cadeia de certificados instalada no seu interior para o IoT Hub. Utilizando o certificado de CA X.509 pré-registado, o IoT Hub pode validar criptograficamente algumas coisas, que a cadeia de certificados carregado é internamente consistente, e que a cadeia foi originada pelo proprietário válido do certificado X.509 CA. Apenas com o processo de registo de CA X.509, o IoT Hub iniciaria um processo de resposta ao desafio de prova de posse para verificar se a cadeia e, portanto, o certificado do dispositivo realmente pertencem ao dispositivo que o carrega. Fá-lo gerando um desafio aleatório a ser assinado pelo dispositivo utilizando a sua chave privada para validação pelo IoT Hub. Uma resposta bem sucedida aciona o IoT Hub para aceitar o dispositivo como autêntico e conceder-lhe a ligação.

No nosso exemplo, cada Smart-X-Widget carregaria o seu certificado exclusivo do dispositivo juntamente com certificados CA Factory-Y e Técnico-Z X.509 e, em seguida, responderia ao desafio de prova de posse do IoT Hub.

![Fluir de um cert para outro, desafio pop do hub](./media/iot-hub-x509ca-concept/device-pop-flow.png)

Note que a base da confiança assenta na proteção de chaves privadas, incluindo chaves privadas do dispositivo. Por isso, não podemos sublinhar suficientemente a importância de chips de silicone seguros sob a forma de Hardware Secure Modules (HSM) para proteger as chaves privadas do dispositivo, e a melhor prática geral de nunca partilhar chaves privadas, como uma fábrica confiando a outra a sua chave privada.
