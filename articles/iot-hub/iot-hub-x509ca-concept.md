---
title: Conceitos de segurança Azure IoT Hub X.509 Microsoft Docs
description: Conceito - compreensão do valor X.509 certificados de autoridade de certificados no fabrico de dispositivos IoT, e autenticação.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 3c7e1167b3326620863d35cb2d4b07235cbd5517
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "61320506"
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>Compreensão conceptual dos certificados De X.509 CA na indústria ioT

Este artigo descreve o valor da utilização de certificados x.509 da autoridade de certificados (CA) no fabrico e autenticação de dispositivos IoT ao IoT Hub. Inclui informações sobre a configuração da cadeia de fornecimento e destaca vantagens.

Este artigo descreve:

* O que x.509 certificados CA são e como obtê-los

* Como registar o seu certificado X.509 CA para o IoT Hub

* Como criar uma cadeia de fornecimento de produção para autenticação baseada em Ca X.509

* Como os dispositivos assinados com X.509 CA ligam-se ao IoT Hub

## <a name="overview"></a>Descrição geral

A autenticação x.509 da Autoridade de Certificados (CA) é uma abordagem para autenticar dispositivos ao IoT Hub utilizando um método que simplifica drasticamente a criação de identidade do dispositivo e a gestão do ciclo de vida na cadeia de fornecimento.

Um atributo diferenciante da autenticação X.509 CA é uma relação de um a muitos que um certificado ca tem com os seus dispositivos a jusante. Esta relação permite o registo de qualquer número de dispositivos no IoT Hub, registando um certificado X.509 CA uma vez, caso contrário os certificados exclusivos do dispositivo devem ser pré-registados para cada dispositivo antes de um dispositivo poder ligar. Esta relação de um a muitos também simplifica as operações de gestão do ciclo de vida dos certificados de vida.

Outro atributo importante da autenticação X.509 CA é a simplificação da logística da cadeia de abastecimento. A autenticação segura dos dispositivos requer que cada dispositivo detenha um segredo único como uma chave como base de confiança. Na autenticação baseada em certificados, este segredo é uma chave privada. Um fluxo típico de fabrico de dispositivos envolve vários passos e guardiões. Gerir chaves privadas do dispositivo de forma segura em vários guardiões e manter a confiança é difícil e dispendioso. A utilização de autoridades de certificados resolve este problema assinando cada guardião numa cadeia de confiança criptográfica em vez de lhes confiar chaves privadas do dispositivo. Cada guardião, por sua vez, assina dispositivos na respetiva fase de processo do fluxo de fabrico. O resultado global é uma cadeia de fornecimento ideal com responsabilidade incorporada através da utilização da cadeia criptográfica de confiança. Vale a pena notar que este processo produz mais segurança quando os dispositivos protegem as suas chaves privadas únicas. Para tal, exortamos a utilização de Módulos Seguros de Hardware (HSM) capazes de gerar chaves privadas internamente que nunca verão a luz do dia.

Este artigo oferece uma visão final da utilização da autenticação X.509 CA, desde a configuração da cadeia de fornecimento até à ligação do dispositivo, ao mesmo tempo que faz uso de um exemplo real para solidificar a compreensão.

## <a name="introduction"></a>Introdução

O certificado X.509 CA é um certificado digital cujo titular pode assinar outros certificados. Este certificado digital é X.509 porque está em conformidade com uma norma de formatação de certificado prescrita pela norma RFC 5280 da IETF, e é uma autoridade de certificados (CA) porque o seu titular pode assinar outros certificados.

A utilização de X.509 CA é melhor compreendida em relação a um exemplo concreto. Considere a Empresa-X, uma fabricante de Smart-X-Widgets projetada para instalação profissional. A empresa-X fornece tanto a produção como a instalação. Contrata o fabricante Factory-Y para fabricar o Smart-X-Widgets, e o prestador de serviços Técnico-Z para instalar. A Empresa-X deseja que o Smart-X-Widget navere diretamente de Factory-Y para Técnico-Z para instalação e que se ligue diretamente ao caso ioT hub da Empresa-X após a instalação sem mais intervenções da Empresa-X. Para que isso aconteça, a Empresa-X precisa de completar algumas operações de configuração única para o Prime Smart-X-Widget para ligação automática. Tendo em mente o cenário de ponta a ponta, o resto deste artigo é estruturado da seguinte forma:

* Adquirir o certificado X.509 CA

* Registe o certificado X.509 CA para o IoT Hub

* Assine os dispositivos numa cadeia de confiança de certificados

* Ligação do dispositivo

## <a name="acquire-the-x509-ca-certificate"></a>Adquirir o certificado X.509 CA

A Empresa-X tem a opção de adquirir um certificado De Ca X.509 a uma autoridade de certificado de raiz pública ou criar um através de um processo auto-assinado. Uma opção seria ideal em relação à outra, dependendo do cenário de candidatura. Independentemente da opção, o processo implica dois passos fundamentais, gerando um par de chaves público/privado e assinando a chave pública num certificado.

![Fluxo para gerar certificados X509CA](./media/iot-hub-x509ca-concept/csr-flow.png)

Os detalhes sobre como realizar estes passos diferem com vários prestadores de serviços.

### <a name="purchasing-an-x509-ca-certificate"></a>Compra de um certificado De Ca X.509

A aquisição de um certificado CA tem o benefício de ter um ato de raiz conhecido como um terceiro de confiança para garantir a legitimidade dos dispositivos IoT quando os dispositivos se conectam. A Empresa-X escolheria esta opção se pretendesse que o Smart-X-Widget interaja com produtos ou serviços de terceiros após a ligação inicial ao IoT Hub.

Para comprar um certificado X.509 CA, a Empresa-X escolheria um prestador de serviços de certificados de raiz. Uma pesquisa na Internet da frase "Root CA" dará boas pistas. A ca raiz irá orientar a Empresa-X sobre como criar o par de chaves público/privado e como gerar um Pedido de Assinatura de Certificado (CSR) para os seus serviços. Uma RSE é o processo formal de pedido de certificado de uma autoridade de certificados. O resultado desta compra é um certificado de utilização como certificado de autoridade. Dada a ubiquidade dos certificados X.509, é provável que o certificado tenha sido devidamente formatado à norma RFC 5280 da IETF.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Criação de um certificado De Ca X.509 Auto-assinado

O processo de criação de um certificado De Ca X.509 Auto-Assinado é semelhante à compra, com exceção de envolver um signatário de terceiros como a autoridade do certificado de raiz. No nosso exemplo, a Empresa-X assinará o seu certificado de autoridade em vez de uma autoridade de certificado de raiz. A Empresa-X pode escolher esta opção para testes até que estejam prontas para comprar um certificado de autoridade. A Empresa-X também pode utilizar um certificado X.509 CA auto-assinado em produção, se o Smart-X-Widget não se destinar a ligar a quaisquer serviços de terceiros fora do IoT Hub.

## <a name="register-the-x509-certificate-to-iot-hub"></a>Registe o certificado X.509 para o IoT Hub

A Empresa-X precisa de registar o X.509 CA para o IoT Hub, onde servirá para autenticar smart-X-Widgets à medida que se conectam. Este é um processo único que permite autenticar e gerir qualquer número de dispositivos Smart-X-Widget. Este processo é uma vez devido a uma relação de um a muitos entre certificado de autoridade e dispositivos e constitui também uma das principais vantagens da utilização do método de autenticação X.509 CA. A alternativa é carregar impressões digitais de certificado individual para cada dispositivo Smart-X-Widget adicionando assim aos custos operacionais.

O registo do certificado X.509 CA é um processo em duas etapas, o carregamento do certificado e o certificado comprovativo de posse.

![Registar um certificado X509CA](./media/iot-hub-x509ca-concept/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>X.509 CA Carregamento de Certificado ca

O processo de upload de certificado S.509 CA X.509 é apenas isso, faça upload do certificado CA para IoT Hub. O IoT Hub espera o certificado num ficheiro. A Empresa-X simplesmente faz o upload do ficheiro de certificado. O ficheiro do certificado NÃO DEVE, em nenhuma circunstância, conter chaves privadas. As melhores práticas das normas que regem a Infraestrutura De Chaves Públicas (PKI) determinam que o conhecimento do privado da Empresa-X neste caso reside exclusivamente no âmbito da Empresa-X.

### <a name="proof-of-possession-of-the-certificate"></a>Prova de posse do certificado

O certificado X.509 CA, como qualquer certificado digital, é informação pública suscetível a escutas. Como tal, um conta-gotas pode intercetar um certificado e tentar carregá-lo como seu. No nosso exemplo, o IoT Hub gostaria de ter a certeza de que o certificado CA que a Empresa-X está a carregar pertence mesmo à Empresa-X. Fá-lo desafiando a Empresa-X a provar que, de facto, possuem o certificado através de um [fluxo de prova de posse (PoP).](https://tools.ietf.org/html/rfc5280#section-3.1) O fluxo de prova de posse implica que o IoT Hub gera um número aleatório a ser assinado pela Company-X usando a sua chave privada. Se a Empresa-X seguisse as melhores práticas da PKI e protegesse a sua chave privada, só eles estariam em posição de responder corretamente ao desafio de prova de posse. O IoT Hub procede ao registo do certificado X.509 CA após uma resposta bem sucedida do desafio de prova de posse.

Uma resposta bem sucedida ao desafio de prova de posse do IoT Hub completa o registo X.509 CA.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>Inscreva dispositivos numa cadeia de confiança de certificados

O IoT requer que todos os dispositivos possuam uma identidade única. Estas identidades constam de certificados de autenticação baseados em certificados. No nosso exemplo, isto significa que todos os Smart-X-Widget devem possuir um certificado de dispositivo único. Como é que a Empresa-X configura isto na sua cadeia de abastecimento?

Uma maneira de fazer isto é pré-gerar certificados para Smart-X-Widgets e confiar o conhecimento de chaves privadas de dispositivo sinuosos correspondentes com parceiros da cadeia de fornecimento. Para a Empresa-X, isto significa confiar a Factory-Y e a Técnica-Z. Embora este seja um método válido, ele vem com desafios que devem ser ultrapassados para garantir a confiança da seguinte forma:

1. Ter de partilhar chaves privadas de dispositivos com parceiros da cadeia de fornecimento, além de ignorar as melhores práticas da PKI de nunca partilhar chaves privadas, torna a confiança na cadeia de fornecimento dispendiosa. Significa que sistemas de capital como salas seguras para alojar chaves privadas de dispositivos, e processos como auditorias periódicas de segurança precisam de ser instalados. Ambos adicionam custos à cadeia de abastecimento.

2. Contabilizar de forma segura os dispositivos da cadeia de fornecimento e, posteriormente, geri-los na implementação torna-se uma tarefa um-para-um para cada par chave-para-dispositivo do ponto de produção de certificado exclusivo do dispositivo (portanto chave privada) para a reforma do dispositivo. Isto impede a gestão de grupos de dispositivos, a menos que o conceito de grupos seja explicitamente integrado no processo de alguma forma. A gestão segura do ciclo de vida e do ciclo de vida do dispositivo torna-se, portanto, um pesado fardo de operações. No nosso exemplo, a Empresa-X suportaria este fardo.

A autenticação do certificado X.509 CA oferece soluções elegantes para desafios listados através da utilização de cadeias de certificados. Uma cadeia de certificados resulta de uma ac assinatura de um CA intermédio que, por sua vez, assina outro CA intermédio e assim continua até que um CA intermediário final assine um dispositivo. No nosso exemplo, a Empresa-X assina a Factory-Y, que por sua vez assina o Técnico-Z que finalmente assina smart-X-Widget.

![Hierarquia da cadeia de certificados](./media/iot-hub-x509ca-concept/cert-chain-hierarchy.png)

Acima da cascata de certificados na cadeia apresenta a entrega lógica da autoridade. Muitas cadeias de abastecimento seguem esta entrega lógica em que cada CA intermediário é assinado na cadeia enquanto recebe todos os certificados ca a montante, e o último CA intermédio finalmente assina cada dispositivo e injeta todos os certificados de autoridade da cadeia para o dispositivo. Isto é comum quando a empresa de fabrico de contratos com uma hierarquia de fábricas encomenda uma determinada fábrica para fazer a fabricação. Embora a hierarquia possa ter vários níveis de profundidade (por exemplo, por geografia/tipo de produto/linha de fabrico), apenas a fábrica no final pode interagir com o dispositivo, mas a cadeia é mantida a partir do topo da hierarquia.

As cadeias alternativas podem ter diferentes ca intermédias interagindo com o dispositivo, caso em que a AC interagindo com o dispositivo injeta conteúdo da cadeia de certificados nessa altura. Os modelos híbridos também são possíveis onde apenas alguns dos CA têm interação física com o dispositivo.

No nosso exemplo, tanto a Factory-Y como a Técnica-Z interagem com o Smart-X-Widget. Embora a Empresa-X detenha o Smart-X-Widget, na verdade não interage fisicamente com ele em toda a cadeia de fornecimento. A cadeia de fidedignidade do Smart-X-Widget compreende, portanto, a assinatura da Empresa-X Factory-Y, que por sua vez assina o Técnico-Z que, em seguida, fornecerá a assinatura final ao Smart-X-Widget. O fabrico e instalação de Smart-X-Widget compreendem a Factory-Y e a Technical-Z utilizando os respetivos certificados ca intermédios para assinar cada Smart-X-Widgets. O resultado final de todo este processo é smart-X-Widgets com certificados de dispositivo exclusivos e cadeia de fidedignidade de certificado salta para o certificado Da Empresa-X CA.

![Cadeia de confiança dos certificados de uma empresa para os certificados de outra empresa](./media/iot-hub-x509ca-concept/cert-mfr-chain.png)

Este é um bom ponto para rever o valor do método X.509 CA. Em vez de pré-gerar e entregar certificados para cada Smart-X-Widget na cadeia de fornecimento, a Empresa-X só teve de assinar factory-Y uma vez. Em vez de ter de rastrear todos os dispositivos ao longo do ciclo de vida do dispositivo, a Empresa-X pode agora rastrear e gerir dispositivos através de grupos que naturalmente emergem do processo da cadeia de fornecimento, por exemplo, dispositivos instalados pela Técnico-Z após julho de algum ano.

Por último, mas não menos importante, o método de autenticação da AC infunde a responsabilidade segura na cadeia de fornecimento de dispositivos. Devido ao processo da cadeia de certificados, as ações de cada membro da cadeia são criptograficamente gravadas e verificáveis.

Este processo baseia-se em certos pressupostos que devem ser surgidos para a completude. Requer a criação independente de um par de chaves público/privado único do dispositivo e que a chave privada seja protegida dentro do dispositivo. Felizmente, os chips de silicone seguros sob a forma de Módulos Seguros de Hardware (HSM) capazes de gerar chaves internamente e proteger chaves privadas existem. A Empresa-X só precisa de adicionar um desses chips na fatura de materiais componentes da Smart-X-Widget.

## <a name="device-connection"></a>Ligação do dispositivo

Secções anteriores acima foram acumuladas até à ligação do dispositivo. Ao simplesmente registar um certificado De Ca X.509 para o IoT Hub uma vez, como é que potencialmente milhões de dispositivos se conectam e são autenticados a partir da primeira vez?  Simples; através do mesmo carregamento de certificado e fluxo de prova de posse que encontramos anteriormente com o registo do certificado X.509 CA.

Os dispositivos fabricados para autenticação X.509 CA estão equipados com certificados exclusivos do dispositivo e uma cadeia de certificados da respetiva cadeia de fornecimento de fabrico. A ligação do dispositivo, mesmo pela primeira vez, acontece num processo em duas etapas: carregamento em cadeia de certificados e prova de posse.

Durante o carregamento da cadeia de certificados, o dispositivo envia o seu certificado único do dispositivo juntamente com a cadeia de certificados instalada no seu interior para o IoT Hub. Utilizando o certificado X.509 CA pré-registado, o IoT Hub pode validar criptograficamente algumas coisas, que a cadeia de certificados carregada é internamente consistente, e que a cadeia foi originada pelo proprietário válido do certificado X.509 CA. Apenas estava com o processo de registo de X.509 CA, o IoT Hub iniciaria um processo de resposta ao desafio comprovativo de posse para verificar se a cadeia e, portanto, o certificado do dispositivo realmente pertencem ao dispositivo que o envia. Fá-lo gerando um desafio aleatório a ser assinado pelo dispositivo utilizando a sua chave privada para validação pelo IoT Hub. Uma resposta bem sucedida desencadeia o IoT Hub para aceitar o dispositivo como autêntico e conceder-lhe ligação.

No nosso exemplo, cada Smart-X-Widget carregaria o seu certificado exclusivo do dispositivo juntamente com certificados De Ca Factory-Y e Técnico-Z X.509 e, em seguida, responderia ao desafio de prova de posse do IoT Hub.

![Fluir de um cert para outro, desafio pop do centro](./media/iot-hub-x509ca-concept/device-pop-flow.png)

Note que a base da confiança depende da proteção de chaves privadas, incluindo chaves privadas do dispositivo. Por isso, não podemos sublinhar suficientemente a importância de chips de silicone seguros sob a forma de Módulos Seguros de Hardware (HSM) para proteger chaves privadas do dispositivo, e a melhor prática geral de nunca partilhar chaves privadas, como uma fábrica que confia outra com a sua chave privada.