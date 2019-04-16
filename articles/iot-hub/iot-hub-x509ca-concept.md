---
title: Conceitos de segurança do Azure IoT Hub X.509 | Documentos da Microsoft
description: Certificados de autoridade em processos de fabrico de dispositivos de IoT e autenticação de certificados do conceito - compreensão do valor X.509.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 3c7e1167b3326620863d35cb2d4b07235cbd5517
ms.sourcegitcommit: e89b9a75e3710559a9d2c705801c306c4e3de16c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2019
ms.locfileid: "59571350"
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>Compreensão conceptual de certificados X.509 de AC no setor de IoT

Este artigo descreve o valor da utilização de certificados de autoridade de certificado X.509 em processos de fabrico de dispositivos de IoT e autenticação para o IoT Hub. Ele inclui informações sobre o fornecimento da cadeia de configuração e destacar as vantagens.

Este artigo descreve:

* Quais são os certificados X.509 de AC e como obtê-los

* Como registar o seu certificado X.509 de AC para o IoT Hub

* Como configurar uma empresa de suprimentos para autenticação baseada em X.509 de AC

* Como os dispositivos que tem sessão iniciados com X.509 de AC ligarem ao IoT Hub

## <a name="overview"></a>Descrição geral

Autenticação de autoridade de certificado (AC) de X.509 é uma abordagem para a autenticação de dispositivos no IoT Hub com um método que simplifica muito a dispositivo criação e o ciclo de vida do gerenciamento de identidades na cadeia de fornecimento.

Um atributo distinção da autenticação X.509 de AC é uma relação um-para-muitos, que um certificado de AC tem com os seus dispositivos de downstream. Esta relação ativa o registo de qualquer número de dispositivos no IoT Hub ao se registrar um certificado X.509 de AC uma vez, caso contrário, certificados de dispositivo exclusivo tem de ser previamente registados para todos os dispositivos antes de pode ligar um dispositivo. Esta relação um-para-muitos também simplifica as operações de gestão de ciclo de vida de certificados de dispositivo.

Outro atributo importante da autenticação X.509 de AC é a simplificação de logística de cadeia de fornecimento. Autenticação segura de dispositivos requer que cada dispositivo contém um segredo exclusivo, como uma chave como base para confiança. Na autenticação baseada em certificados, o segredo é uma chave privada. Um dispositivo típico de fluxo de fabrico envolve vários passos e custodians. Em segurança gerir chaves privadas de dispositivo entre vários custodians e manter a confiança é difícil e dispendioso. Utilizar autoridades de certificação resolve esse problema ao iniciar cada conservador numa cadeia de fidedignidade de criptografia, em vez de confiando-os com chaves privadas do dispositivo. Cada conservador por sua vez se inscreve dispositivos em sua etapa de processo respectivos do fluxo de fabrico. O resultado geral é uma cadeia de fornecimento ideal com responsabilidade incorporada por meio do uso da criptografia cadeia de confiança. Vale a pena observar que este processo gera a maior segurança quando dispositivos protege as chaves privadas exclusivas. Para este fim, recomendamos vivamente a utilização de Hardware seguro módulos (HSM) capaz de gerar internamente chaves privadas que nunca irão ver a luza do dia.

Este artigo oferece uma visão de ponto a ponto da utilização da autenticação X.509 de AC, da configuração de cadeia de suprimentos para ligação do dispositivo, utilizando a utilização de um exemplo do mundo real para se consolidar compreensão.

## <a name="introduction"></a>Introdução

O certificado X.509 de AC é um certificado digital cujo titular pode assinar outros certificados. Este certificado digital é X.509 porque está em conformidade com um certificado de formatação padrão prescrita pelo padrão de RFC 5280 da IETF e é uma autoridade de certificação (AC), uma vez que o titular pode assinar outros certificados.

A utilização de X.509 de AC é mais bem compreendida em relação a um exemplo concreto. Considere a empresa-X, um criador de Smart-X-Widgets, concebido para a instalação do profissional. Empresa-X terceiriza a produção e de instalação. Contratos de fabricante Factory-Y para fabricação Smart-X-Widgets e o fornecedor de serviços técnico Z para instalar. Empresa-X deseja que Smart-X-Widget diretamente é fornecido de fábrica-Y a Z técnico para a instalação e que se liga diretamente para a empresa-x a instância do IoT Hub após a instalação sem qualquer outra intervenção da empresa X. Para que isso aconteça, a empresa X tem de concluir algumas operações de configuração de uso individual avisar Smart-X-Widget para ligação automática. Com o cenário de ponta a ponta em mente, o restante deste artigo está estruturado da seguinte forma:

* Adquirir o certificado X.509 de AC

* Registar certificado X.509 de AC para o IoT Hub

* Dispositivos de início de sessão numa cadeia de certificados de confiança

* Ligação do dispositivo

## <a name="acquire-the-x509-ca-certificate"></a>Adquirir o certificado X.509 de AC

Empresa-X tem a opção de adquirir um certificado X.509 de AC de uma autoridade de certificação de raiz público ou criar um através de um processo autoassinado. Uma opção seria ideal em detrimento do outro, dependendo do cenário de aplicação. Independentemente da opção, o processo exige duas etapas fundamentais, gerar um par de chaves públicas/privadas e a chave pública para um certificado de assinatura.

![Flow para gerar um certificados X509CA](./media/iot-hub-x509ca-concept/csr-flow.png)

Detalhes sobre como realizar estes passos são diferentes com vários provedores de serviços.

### <a name="purchasing-an-x509-ca-certificate"></a>Adquirir um certificado X.509 de AC

Comprar um certificado de AC tem a vantagem de ter um ato de AC de raiz bem conhecido como um terceiro confiável para comprovar a legitimidade dos dispositivos de IoT quando os dispositivos se ligam. Empresa-X seria Escolha esta opção se pretendem Smart-X-Widget para interagir com produtos de terceiros ou serviços após a ligação inicial ao IoT Hub.

Para comprar um certificado X.509 de AC, a empresa X escolheria um fornecedor de serviços de certificados de raiz. Uma pesquisa na internet para a AC de raiz de expressão resulta em oportunidades potenciais boa. A AC de raiz serve como guia empresa X sobre como criar o par de chaves públicas/privadas e como gerar um certificado de assinatura do pedido (CSR) para seus serviços. Um CSR é o processo formal de aplicação para um certificado de uma autoridade de certificação. O resultado desta compra é um certificado para utilização como um certificado de autoridade. O certificado devido a universalidade de certificados X.509, é provável que ter sido corretamente formatado para RFC 5280 da IETF padrão.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Criação de um certificado autoassinado X.509 de AC

O processo para criar um certificado autoassinado X.509 de AC é semelhante à aquisição de com a exceção que envolvem um signatário de terceiros, como a autoridade de certificação de raiz. No nosso exemplo, a empresa X assinará seu certificado de autoridade, em vez de uma autoridade de certificação de raiz. Empresa-X pode escolher esta opção para testar até que estejam prontas comprar um certificado de autoridade. Empresa-X também poderá utilizar um certificado de AC X.509 autoassinado na produção, se o Smart-X-Widget não se destina a estabelecer ligação a quaisquer serviços de terceiros fora do IoT Hub.

## <a name="register-the-x509-certificate-to-iot-hub"></a>Registre-se o certificado X.509 para o IoT Hub

Empresa-X precisa se registrar X.509 de AC para o IoT Hub em que servirá para autenticar o Smart-X-Widgets porque estes se ligam. Este é um processo único, que permite a capacidade autenticar e gerir qualquer número de dispositivos de Smart-X-Widget. Este processo é a única devido a uma relação um-para-muitos entre o certificado de autoridade e de dispositivos e também é uma das principais vantagens de usar o método de autenticação X.509 de AC. A alternativa é carregar thumbprints de certificados individuais para cada dispositivo de Smart-X-Widget, deste modo, adicionando os custos operacionais.

Registo do certificado X.509 de AC é um processo de dois passos, o carregamento do certificado e uma prova de posse da certificado.

![Registar um certificado de X509CA](./media/iot-hub-x509ca-concept/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>Carregamento de certificados de AC X.509

O certificado X.509 de AC do processo de carregamento é apenas isso, carregue o certificado de AC para o IoT Hub. IoT Hub espera que o certificado num arquivo. Empresa-X simplesmente carrega o ficheiro de certificado. O ficheiro de certificado não deve em nenhuma circunstância contém chaves privadas. Melhores práticas de normas que regem a infraestrutura de chaves públicas (PKI) estipula esse conhecimento da empresa-x privada reside nesse caso exclusivamente dentro da empresa X.

### <a name="proof-of-possession-of-the-certificate"></a>Prova de posse do certificado

O certificado X.509 de AC, tal como qualquer certificado digital, é públicas informações que são suscetíveis a interceptação. Como tal, um interceptador pode interceptar um certificado e tente carregar os seus próprios. No nosso exemplo, o IoT Hub gostaria Certifique-se de que o certificado da AC de que empresa-X está a carregar realmente pertença à empresa X. Ele faz então por um desafio de empresa-X para provar que eles de fato que possuem o certificado através de um [fluxo de prova de posse (PoP)](https://tools.ietf.org/html/rfc5280#section-3.1). O fluxo de prova de posse envolve o IoT Hub gerar um número aleatório sejam assinados pela empresa-X, utilizando a respetiva chave privada. Se empresa X seguido as práticas recomendadas do PKI e protegida a chave privada, em seguida, apenas ficariam em posição para responder corretamente para o desafio de prova de posse. IoT Hub continua a registar o certificado de AC X.509 após uma resposta com êxito do desafio de prova de posse.

Uma resposta com êxito para o desafio de prova de posse do IoT Hub é concluído o registo de AC X.509.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>Dispositivos de início de sessão numa cadeia de certificados de confiança

IoT requer que cada dispositivo possuir uma identidade exclusiva. Estas identidades estão os certificados de formulário para esquemas de autenticação baseada em certificados. No nosso exemplo, isso significa que todos os Smart-X-Widget deve possuir um certificado de dispositivo exclusivo. Como empresa X configuração para que isso na sua cadeia de suprimentos?

Uma forma de fazer isso é gerar previamente os certificados de Smart-X-Widgets e confiando a dados de conhecimento de dispositivo exclusivo chaves privadas correspondentes com parceiros de cadeia de fornecimento. Para empresa-X, isso significa confiando a fábrica-Y e Z técnico. Embora esse seja um método válido, trata-se com os desafios que devem ser ultrapassados para garantir a confiança da seguinte forma:

1. Ter de partilhar as chaves privadas de dispositivo com os parceiros de cadeia de fornecimento, além de PKI a ignorar de melhores práticas de nunca partilha as chaves privadas, torna o desenvolvimento de confiança na cadeia de fornecimento cara. Isso significa que os sistemas de capitais, como salas de seguros para chaves privadas de dispositivos de casa e processos como auditorias de segurança periódica tem de ser instalado. Ambos adicionar o custo para a cadeia de fornecimento.

2. Gestão de contas com segurança para dispositivos na cadeia de fornecimento e gerenciá-los mais tarde na implementação torna-se uma tarefa um para um para cada par de chave para o dispositivo a partir do ponto de geração de (chave privada, por conseguinte,) do certificado exclusivo de dispositivo para extinção de dispositivos. Isto impede a gestão de grupos de dispositivos, a menos que o conceito de grupos explicitamente baseia-se no processo de alguma forma. Gestão de ciclo de vida do dispositivo e de gestão de contas segura, por isso, torna-se uma carga pesada de operações. No nosso exemplo, a empresa X seria tenha essa carga.

Autenticação de certificados X.509 de AC oferece soluções elegantes para afore listados desafios com o uso de cadeias de certificados. Uma cadeia de certificados resulta de uma AC assinatura de uma AC intermédia que por sua vez, inicia outra AC intermediária e por isso continua até que uma AC intermédia final se inscreve um dispositivo. No nosso exemplo, a empresa X assina Factory-Y, que por sua vez, inicia técnico-Z, que finalmente assina Smart-X-Widget.

![Hierarquia de cadeia de certificados](./media/iot-hub-x509ca-concept/cert-chain-hierarchy.png)

Acima cascade dos certificados na cadeia apresenta a lógica paraoperações de autoridade. Muitas cadeias de fornecimento siga esta lógica paraoperações no qual cada AC intermédia é conectado a cadeia de ao receber todos os certificados de AC a montante e a última AC intermediária, finalmente, assina cada dispositivo e inserir todos os certificados de autoridade da cadeia de no dispositivo. Isso é comum quando a empresa de fabrico do contrato com uma hierarquia de fábricas de commissions um alocador específico para fazer o fabrico. Enquanto a hierarquia pode ter vários níveis de profunda (por exemplo, por geografia/produto tipo/linha de produção), apenas a fábrica no final obtém para interagir com o dispositivo, mas a cadeia é mantida na parte superior da hierarquia.

Cadeias alternativas podem ter diferente AC intermediária, interagir com o dispositivo no qual caso a interação de AC com o dispositivo injeta conteúdo de cadeia de certificado nesse momento. Modelos híbridos também são possíveis em que apenas algumas da AC tem física interação com o dispositivo.

No nosso exemplo, fábrica-Y e Z técnico de interagirem com o Smart-X-Widget. Enquanto a empresa X é a proprietária de Smart-X-Widget, ele realmente não fisicamente interagir com o mesmo na cadeia de fornecimento de todo. A cadeia de certificados de confiança para Smart-X-Widget compõem, portanto, a empresa X Factory-Y que por sua vez, inicia técnico-Z, em seguida, irá fornecer, assinatura final Smart-X-Widget de assinatura. O fabricante e a instalação de Smart-X-Widget consistem em Y de fábrica e usar seus respectivos certificados de AC intermediária para assinar cada Smart-X-Widgets técnico-Z. O resultado final de todo este processo é Smart-X-Widgets com certificados de dispositivo exclusivo e a cadeia de certificados de confiança, avance para o certificado da AC de empresa X.

![Cadeia de fidedignidade de certificados de uma empresa para os certificados de outra empresa](./media/iot-hub-x509ca-concept/cert-mfr-chain.png)

Este é um bom ponto de rever o valor do método X.509 de AC. Em vez de gerar previamente e signifique entregar certificados para todos os Smart-X-Widget, para a cadeia de fornecimento, empresa X tinha apenas Factory-Y de iniciar sessão uma vez. Em vez de precisar controlar todos os dispositivos durante o ciclo de vida do dispositivo, da empresa-X agora pode controlar e gerir dispositivos através de grupos que naturalmente emerge o processo de cadeia de fornecimento, por exemplo, dispositivos instalados por técnico-Z, depois de Julho de um ano.

Por último, mas não menos importante, o método de AC de autenticação infuses responsabilidade de segura para o cadeia de fornecimento de fabrico de dispositivos. Devido ao processo de cadeia de certificados, as ações de cada membro da cadeia é criptograficamente gravados e verificáveis.

Este processo se baseia em certos pressupostos que devem ser reproduzidos para ser completo. Ele requer a criação independente de dispositivo exclusivo públicas/privadas par de chaves e que a chave privada protegidos dentro do dispositivo. Felizmente, existem chips de silicon seguro na forma de Hardware seguro módulos (HSM) capaz de internamente a gerar chaves e proteger chaves privadas. Empresa-X só precisa de adicionar um dos chips de tais componente de lista do Smart-X-Widget de materiais.

## <a name="device-connection"></a>Ligação do Dispositivo

As secções anteriores acima tem foi aumentando para ligação do dispositivo. Registrando-se simplesmente um certificado X.509 de AC para o IoT Hub um tempo, como potencialmente milhões de dispositivos ligar e obter autenticado na primeira vez?  Simples; através do mesmo carregar o certificado e o fluxo de prova de posse encontrámos anteriormente com o registo do certificado X.509 de AC.

Cadeia de fornecimento de fabrico de dispositivos fabricados para autenticação X.509 de AC estão equipados com certificados de dispositivo exclusivo e uma cadeia de certificados a partir de seus respectivos. Ligação do dispositivo, mesmo para na primeira vez, ocorre num processo de dois passos: carregamento de cadeia e uma prova de posse de certificado.

Durante o carregamento de cadeia de certificado, o dispositivo carrega seu certificado de dispositivo exclusivo, juntamente com a cadeia de certificados instalado no mesmo para o IoT Hub. Utilizar o certificado de AC X.509 previamente registado, IoT Hub pode validar criptograficamente algumas coisas, que a cadeia de certificados carregados é internamente consistente e de que a cadeia originada pelo proprietário do certificado X.509 de AC válido. Foi apenas com o processo de registo de AC X.509, o IoT Hub poderia iniciar um processo de resposta de desafio de prova de posse para determinar que a cadeia e, por conseguinte, um certificado de dispositivo, na verdade, pertence ao dispositivo carregá-lo. Ele faz isso gerando um desafio aleatório sejam assinados pelo dispositivo utilizando a respetiva chave privada para a validação pelo IoT Hub. Uma resposta com êxito aciona o IoT Hub para aceitar o dispositivo como autêntico e conceda-ligação.

No nosso exemplo, cada Smart-X-Widget seria carregar seu certificado de dispositivo exclusivo, juntamente com os certificados de fábrica-Y e Z técnico X.509 de AC e, em seguida, responder ao desafio de prova de posse do IoT Hub.

![Fluxo a partir de um certificado para outro, POP-desafio do hub](./media/iot-hub-x509ca-concept/device-pop-flow.png)

Tenha em atenção que a base de confiança assenta proteger chaves privadas, incluindo as chaves privadas do dispositivo. Nós não é possível, por conseguinte, estresse suficiente a importância de silicon seguro chips na forma de Hardware seguro módulos (HSM) para proteger chaves privadas de dispositivo e o procedimento geral recomendado de compartilhamento nunca chaves privadas, como uma fábrica confiando a outra com o chave privada.