---
title: Política de Mensagens
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre as políticas de mensagens SMS.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/19/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: bb9765c2620f45d67bf888f8bfe8a4dee450cfd6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646890"
---
# <a name="azure-communication-services-messaging-policy"></a>Política de Mensagens de Serviços de Comunicação da Azure

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

A Azure Communication Services está a transformar a forma como os nossos clientes se envolvem com os seus clientes, construindo experiências de comunicação ricas e personalizadas que tiram partido dos mesmos serviços de qualidade empresarial que apoiam a Microsoft Teams e o Skype. Integre a funcionalidade de mensagens SMS nas suas soluções de comunicações para chegar aos seus clientes a qualquer momento e em qualquer lugar que precisem de apoio. Só precisas de ter em mente alguns requisitos de mensagens para começar.

Sabemos que os requisitos de mensagens podem parecer assustadores de aprender, mas são tão fáceis como lembrar "COMS":

- C - Consentimento
- O - Opt-Out
- M - Conteúdo de mensagem
- S - Falsificação

Desenvolvemos esta política de mensagens para ajudá-lo a satisfazer os requisitos regulamentares e alinhar-se com as melhores práticas recomendadas. 

[!INCLUDE [Notice](../../includes/messaging-policy-include.md)]

## <a name="consent"></a>Consent (Consentimento) 

### <a name="what-is-consent"></a>O que é consentimento?

O Consentimento é um acordo entre si e o destinatário da mensagem que lhe permite enviar mensagens automatizadas para eles. Deve obter o consentimento antes de enviar a primeira mensagem, e deve deixar claro ao destinatário que eles estão concordando em receber mensagens suas. Este procedimento é conhecido como recebendo "consentimento expresso prévio" do indivíduo que pretende enviar mensagem.

As mensagens que envia devem ser o mesmo tipo de mensagens que o destinatário aceitou receber e só devem ser enviadas para o número que o destinatário lhe forneceu. Se pretender enviar mensagens informativas, como lembretes de marcação ou alertas, então o consentimento pode ser escrito ou oral. Se pretende enviar mensagens promocionais, como mensagens de vendas ou marketing que promovam um produto ou serviço, então o consentimento deve ser escrito.

### <a name="how-do-you-obtain-consent"></a>Como obtém o seu consentimento?

O consentimento pode ser obtido de várias formas, tais como:

- quando um utilizador introduz o seu número de telefone num website, 
- quando um utilizador inicia uma troca de mensagens de texto, ou
- quando um utilizador envia uma palavra-chave de inscrição para o seu número de telefone. 
 
Independentemente da forma como o consentimento é obtido, você e os seus clientes devem garantir que o consentimento é inequívoco. O âmbito do consentimento deve ser claro para o destinatário.


### <a name="consent-requirements"></a>Requisitos de consentimento:

- Forneça uma "Chamada à Ação" antes de obter o consentimento. Você e os seus clientes devem fornecer aos potenciais destinatários de mensagens um "call to action" que os convida a optar pelo seu programa de mensagens. O apelo à ação deve incluir, no mínimo: (1) a identidade do remetente de mensagens, (2) instruções claras de opt-in, (3) instruções de opt-out e (4) quaisquer taxas de mensagens associadas.
- O consentimento não é transferível ou atribuível. Qualquer consentimento que um indivíduo lhe forneça não pode ser transferido ou vendido a terceiros não filiados. Se recolher o consentimento de um indivíduo para um terceiro, então deve identificar claramente o terceiro para o indivíduo. Deve também indicar que o consentimento que obteve se aplica apenas às comunicações de terceiros.
- O consentimento é limitado no propósito. Um indivíduo que forneça o seu número para um determinado propósito consente em receber comunicações apenas para esse fim específico e a partir desse remetente de mensagens específicas. Antes de obter o consentimento, deve notificar claramente o destinatário da mensagem pretendido se enviar mensagens ou mensagens recorrentes de uma afiliada.

### <a name="consent-best-practices"></a>Consentimento boas práticas:

Além dos requisitos de mensagens acima discutidos, pode querer implementar várias boas práticas comuns, incluindo: 

- Informação detalhada de "Chamada à Ação". Para garantir que obtém o consentimento adequado, forneça
  - o nome ou descrição do seu programa de mensagens ou produto
  - o número(s) a partir do qual os destinatários receberão mensagens, e 
  - quaisquer termos e condições aplicáveis antes de um indivíduo optar por receber mensagens suas.
- Registos precisos de consentimento. Deve reter registos de qualquer consentimento que um indivíduo lhe dê durante pelo menos quatro anos. Os registos de consentimento podem incluir:
  - mps de tempo
  - o meio pelo qual o consentimento foi obtido
  - a campanha específica para a qual o consentimento foi obtido
  - capturas de tela
  - o endereço de identificação ou IP da sessão do indivíduo que consente.
- Política de privacidade e segurança. Os desenvolvedores são encorajados a fornecer políticas de privacidade simples que os destinatários de mensagens podem rever antes do seu consentimento ser obtido. Recomendamos também a manutenção de controlos de segurança pró-ativos para salvaguardar a informação privada dos indivíduos.


## <a name="double-opt-in-consent"></a>Consentimento duplo de opt-In:

A Azure Communication Services recomenda que utilize o consentimento duplo para todas as campanhas de mensagens. O consentimento duplo de opt-in é um processo em duas etapas em que um indivíduo primeiro dá consentimento para receber certos tipos de mensagens de si. Em seguida, envie uma mensagem de opt-in de acompanhamento para confirmar o seu consentimento. Só deverá enviar mais mensagens quando o destinatário da mensagem confirmar o seu consentimento.

A mensagem de confirmação inicial que envia deve incluir a sua identidade, a opção de excluir mensagens futuras (como a utilização de um comando "STOP"), um número gratuito ou um comando "HELP" para informações adicionais, notificação de que o indivíduo está inscrito num programa de mensagens recorrentes, uma breve descrição do programa, a frequência com que pretende enviar mensagens recorrentes , e quaisquer taxas associadas. 

### <a name="does-azure-communication-services-ever-require-double-opt-in-consent"></a>Os Serviços de Comunicação Azure alguma vez requerem o consentimento duplo de opt-in?
Sim, embora o consentimento duplo de opt-in seja sempre recomendado, a Azure Communication Services exige que use o consentimento duplo de opt-in para alguns tipos de campanhas de mensagens devido ao seu uso frequente em esquemas de phishing ou à sua tendência para resultar em queixas de consumidores. Estas campanhas incluem:
- Mensagens de garantia automática
- Planos de seguro de saúde de curto prazo
- Refinanciamento da dívida ou mensagens de redução da taxa de juro se não for feita por uma instituição financeira
- Mensagens de geração de chumbo
- Sorteios, concursos e brindes
- Ofertas de trabalho a partir de casa

As campanhas para as quais são necessárias duplas autorizações de opt-in estão sujeitas a alterações ao critério dos Serviços de Comunicação Azure.

### <a name="exceptions-to-traditional-consent-rules"></a>Exceções às regras tradicionais de consentimento:
Embora o consentimento expresso prévio seja normalmente necessário antes de enviar uma mensagem, existem duas situações em que o consentimento para enviar uma mensagem a um indivíduo está implícito.

- O destinatário inicia uma comunicação. Se um indivíduo iniciar uma comunicação enviando-lhe uma mensagem, então poderá fornecer informações relevantes em resposta a um inquérito ou pedido específico contido na mensagem. No entanto, o consentimento implícito que o indivíduo fornecido limita-se à conversa que o indivíduo iniciou, a menos que obtenha o consentimento para mais comunicações.
- Isenções para serviços específicos. Existem vários serviços específicos para os quais pode ter o consentimento implícito para iniciar uma mensagem. O mais comum são: 
- mensagens de entrega de pacote
- mensagens de instituições financeiras que dizem respeito a tópicos sensíveis ao tempo (tais como transações potencialmente fraudulentas ou violações de dados)
- mensagens de prestadores de cuidados de saúde que incluem informações sensíveis ao tempo e um propósito de tratamento (como lembretes de nomeação ou exame, resultados do laboratório e notificações de prescrição). 
 
Nenhuma destas mensagens pode incluir solicitações ou anúncios.


## <a name="opt-out"></a>Opt-out

Os destinatários da mensagem podem revogar o consentimento e excluir a receção de mensagens futuras através de quaisquer meios razoáveis. Não pode designar um meio exclusivo para os destinatários da mensagem revogarem o consentimento. 

### <a name="opt-out-requirements"></a>Requisitos de opt-out:

Certifique-se de que os destinatários de mensagens podem excluir as mensagens futuras a qualquer momento. Também deve oferecer várias opções de opt-out. Depois de um destinatário de mensagem optar por sair, não deve enviar mensagens adicionais a menos que o indivíduo forneça um consentimento renovado.

Um dos mecanismos de opt-out mais comuns é incluir uma palavra-chave "STOP" na mensagem inicial de cada nova conversa. Esteja preparado para remover os clientes que respondam com um "stop" minúsculo ou outras palavras-chave comuns, tais como "cancelar" ou "cancelar". Após um consentimento individual, deve removê-las de todas as campanhas de mensagens recorrentes, a menos que optem expressamente por continuar a receber mensagens de um determinado programa.

### <a name="opt-out-best-practices"></a>Opt-out melhores práticas:

Além das palavras-chave, outros mecanismos comuns de opt-out incluem fornecer aos clientes um endereço de e-mail de opt-out designado, o número de telefone do pessoal de apoio ao cliente, ou um link para cancelar a subscrição na sua página web. 


### <a name="how-we-handle-opt-out-requests"></a>Como lidamos com pedidos de opt-out:

Se um indivíduo solicitar a exclusão de mensagens futuras num número gratuito dos Serviços de Comunicação Azure, todo o tráfego adicional desse número será automaticamente interrompido. No entanto, deve ainda garantir que não envia mensagens adicionais para essa campanha de mensagens a partir de números novos ou diferentes. Se obteve o consentimento expresso para uma campanha de mensagens diferente, então pode continuar a enviar mensagens de um número diferente para essa campanha. Consulte a nossa secção de PERGUNTAS EQ para saber mais sobre [o tratamento de opt-out](https://github.com/Prakulka/azure-docs-pr/blob/master/articles/communication-services/concepts/telephony-sms/sms-faq.md#how-can-i-receive-messages-using-azure-communication-services)

## <a name="message-content"></a>Conteúdo de mensagens

### <a name="adult-content"></a>Conteúdo adulto:

O conteúdo da mensagem que inclui elementos de sexo, ódio, álcool, armas de fogo, tabaco, jogo ou sorteios e concursos pode desencadear requisitos adicionais. Este conteúdo é expressamente proibido em algumas jurisdições. Se enviar uma mensagem que inclua este conteúdo, então é seu dever respeitar todas as leis aplicáveis das jurisdições em que as comunicações são recebidas. A pedido da aplicação da lei ou dos Serviços de Comunicação Azure, deve estar preparado para fornecer provas de consentimento com as leis locais que regulam o conteúdo dos adultos.

Mesmo quando esse conteúdo não for ilegal, deve incluir um mecanismo de verificação de idade no opt-in para o portão de idade o destinatário da mensagem pretendida a partir do conteúdo adulto. Nos Estados Unidos, aplicam-se requisitos legais adicionais às comunicações de marketing dirigidas a crianças menores de 13 anos. 

### <a name="prohibited-content"></a>Conteúdo proibido:

A Azure Communication Services proíbe determinados conteúdos de mensagens independentemente do consentimento. O conteúdo proibido inclui:
- Conteúdo que promova atividades ilícitas (por exemplo, evasão fiscal ou crueldade com animais nos Estados Unidos)
- Discurso de ódio, discurso difamatório, assédio ou outro discurso determinado a ser manifestamente ofensivo
- Conteúdo pornográfico
- Conteúdo obsceno ou vulgar
- Intimidação e ameaças
- Conteúdo que pretende defraudar, enganar, causar danos, ou obter indevidamente qualquer coisa de valor 
- Conteúdo que incita a danos, discriminação ou violência
- Conteúdo que espalha malware
- Conteúdo que pretende evitar requisitos de gating de idade

Reservamo-nos o direito de modificar a lista de conteúdos de mensagens proibidas a qualquer momento.

## <a name="spoofing"></a>Spoofing

Falsificar é o ato de causar uma visualização enganosa ou imprecisa de um número de origem enganoso ou impreciso no dispositivo do destinatário da mensagem. Desencorajamos fortemente a si e a qualquer prestador de serviços que utilize de enviar mensagens falsificadas. A falsificação protege a identidade do remetente de mensagens e impede que os destinatários de mensagens facilmente optem por comunicações indesejadas. Também exigimos que cumpra todas as leis de falsificação aplicáveis.

## <a name="final-thoughts"></a>Pensamentos finais

### <a name="legal-responsibility"></a>Responsabilidade Legal:

Esta Política de Mensagens não constitui aconselhamento jurídico, e reservamo-nos o direito de modificar a política a qualquer momento. A Azure Communication Services não é responsável por garantir que o conteúdo, o tempo ou os destinatários das mensagens dos nossos clientes satisfaçam todos os requisitos legais aplicáveis. 

Os nossos clientes são responsáveis por todos os requisitos de mensagens. Se é uma plataforma ou fornecedor de software que utiliza serviços de comunicação Azure para fins de mensagens, então deve exigir que os seus clientes também respeitem todos os requisitos discutidos nesta Política de Mensagens. Para mais orientações, o CTIA fornece princípios de mensagens úteis [e boas práticas.](https://api.ctia.org/wp-content/uploads/2019/07/190719-CTIA-Messaging-Principles-and-Best-Practices-FINAL.pdf)

### <a name="penalties"></a>Penalidades:

Encorajamos os nossos clientes a desenvolver e implementar políticas e procedimentos concebidos para garantir o cumprimento de todos os requisitos de mensagens. Violações dos requisitos de mensagens podem levar a multas substanciais que podem balão rapidamente. É do seu interesse aprender e respeitar todos os requisitos de mensagens aplicáveis e desenvolver salvaguardas de mitigação eficazes para conter e eliminar violações antes de se espalharem. Se violar a nossa Política de Mensagens ou outros requisitos legais, trabalharemos consigo para garantir o cumprimento futuro. No entanto, reservamo-nos o direito de remover qualquer cliente da plataforma Azure Communication Services que demonstre um padrão de incumprimento da nossa Política de Mensagens ou requisitos legais.
