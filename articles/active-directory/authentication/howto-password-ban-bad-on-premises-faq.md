---
title: Proteção de passe do Azure AD no local FAQ
description: Proteção de passe do Azure AD no local FAQ
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91ab8e8757c4a5313fde5f4d883e45648c9143b7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57901360"
---
# <a name="preview-azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Pré-visualização: Azure proteção de palavra-passe do AD no local - perguntas mais frequentes

|     |
| --- |
| Proteção de palavra-passe do Azure AD é uma funcionalidade de pré-visualização pública do Azure Active Directory. Para obter mais informações sobre pré-visualizações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="general-questions"></a>Perguntas gerais

**P: Que orientação devem os utilizadores receber sobre como selecionar uma palavra-passe segura?**

Documentação de orientação atual da Microsoft sobre esse tópico pode ser encontrada na seguinte hiperligação:

[Orientações de palavra-passe do Microsoft](https://www.microsoft.com/en-us/research/publication/password-guidance)

**P: É no local suportada em nuvens não pública de proteção de palavra-passe do Azure AD?**

Não, a proteção de palavra-passe do Azure AD no local só é suportada na cloud pública. Já foi anunciada sem data de disponibilidade da cloud não públicas.

**P: Como posso aplicar os benefícios da proteção de palavra-passe do Azure AD para um subconjunto dos meus utilizadores no local?**

Não suportado. Depois de implementada e ativada, a proteção de palavra-passe do Azure AD não distinguir - todos os utilizadores recebem benefícios de segurança igual.

**P: O que é a diferença entre uma alteração de palavra-passe e um conjunto de palavra-passe (ou repor)?**

Uma alteração de palavra-passe é quando o utilizador seleciona uma nova palavra-passe depois de comprovar a têm conhecimento da palavra-passe antiga. Por exemplo, este é o que acontece quando um utilizador inicia sessão no Windows e, em seguida, é solicitado a escolher uma nova palavra-passe.

Um conjunto de palavra-passe (por vezes denominado uma reposição de palavra-passe) é quando um administrador substitui a palavra-passe numa conta com uma nova palavra-passe, por exemplo, utilizando a ferramenta de gerenciamento do Active Directory utilizadores e computadores. Esta operação requer um alto nível de privilégio (normalmente, o administrador de domínio) e a pessoa que efetua a operação, normalmente, não tem conhecimento da palavra-passe antiga. Cenários de assistência técnica, muitas vezes, fazem-lo, por exemplo quando um usuário que esqueceu a palavra-passe a prestar assistência. Também poderá ver a palavra-passe definida eventos quando uma nova conta de utilizador está a ser criada pela primeira vez com uma palavra-passe.

A política de validação da palavra-passe se comporta iguais, independentemente de se está a ser feito uma alteração de palavra-passe ou um conjunto. O serviço de agente do Azure AD palavra-passe DC de proteção de registrar eventos diferentes para o informar se uma alteração de palavra-passe ou a operação de definição foi feita.  Ver [proteção de palavra-passe do Azure AD, monitorização e registo](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor).

**P: É possível para instalar a proteção de palavra-passe do Azure AD lado a lado com outros produtos baseados em palavra-passe-filtro?**

Sim. Suporte para várias dlls de filtro de palavra-passe registado é uma funcionalidade de Windows core e não específico para a proteção de palavra-passe do Azure AD. Todas as dlls de filtro de palavras-passe registado tem de concordar antes de uma palavra-passe é aceite.

**P: Como implementar e configurar a proteção de palavra-passe do Azure AD no meu ambiente do Active Directory sem utilizar o Azure?**

Não suportado. Proteção de palavra-passe do AD do Azure é uma funcionalidade do Azure que suporta a ser expandidos para o ambiente do Active Directory no local.

**P: Como posso modificar o conteúdo da política ao nível do Active Directory?**

Não suportado. A política pode ser administrada apenas com o portal de gestão do Azure AD. Consulte também a pergunta anterior.

**P: Por que motivo o DFSR é necessário para a replicação de sysvol?**

O FRS (a tecnologia antecessora da DFSR) tem vários problemas conhecidos e é totalmente suportado em versões mais recentes do Windows Server Active Directory. Zero testes de proteção de palavra-passe do Azure AD serão efetuada em domínios configurados do FRS.

Para obter mais informações, consulte os artigos seguintes:

[O caso para replicação de sysvol de migrar para o DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[O End é Nigh para FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**P: Quanto espaço em disco a funcionalidade requer na partilha de sysvol de domínio?**

A utilização de espaço precisa varia, uma vez que depende de fatores como o número e o comprimento dos tokens Banidos na lista banida global Microsoft e a lista personalizada de por inquilino, além de sobrecarga de encriptação. O conteúdo destas listas é suscetível de aumentar no futuro. Com isso em mente, uma expectativa razoável de é que a funcionalidade tem de, pelo menos, cinco (5) megabytes de espaço na partilha de sysvol de domínio.

**P: Por que motivo é necessário um reinício para instalar ou atualizar o software do agente DC?**

Este requisito é causado por núcleo de comportamento do Windows.

**P: Existe alguma forma de configurar um agente de controlador de domínio para utilizar um servidor proxy específico?**

Não. Uma vez que o servidor proxy é sem monitoração de estado, não é importante que servidor de proxy específico é utilizado.

**P: É muito bem implementar o serviço de Proxy de proteção de palavra-passe do Azure AD lado a lado com outros serviços, como o Azure AD Connect?**

Sim. O serviço de Proxy de proteção de palavra-passe do Azure AD e do Azure AD Connect devem nunca entre em conflito diretamente entre si.

**P: Por que ordem devem os agentes do DC e os proxies de ser instalados e registados?**

Qualquer ordenação de instalação do agente de Proxy, a instalação do agente DC, o registo de floresta e o registo de Proxy é suportada.

**P: Eu deveria estar preocupado com o impacto em meu controladores de domínio de implementar esta funcionalidade no desempenho?**

O serviço de agente do Azure AD palavra-passe DC de proteção não deve afetar significativamente o desempenho do controlador de domínio numa implementação de bom estado de funcionamento do Active Directory existente.

Palavra-passe da implementações do Active Directory a maioria das operações de alteração são uma pequena proporção da carga de trabalho geral em qualquer controlador de domínio especificado. Por exemplo, imagine um domínio do Active Directory com 10000 contas de utilizador e uma política de MaxPasswordAge definido como 30 dias. Em média, este domínio verá 10000/30 = ~ 333 operações de alteração de palavra-passe por dia, o que é um número menor de operações com até mesmo um único controlador de domínio. Considere um cenário mais desfavorável potencial: vamos supor que esses 333 ~ alterações de palavra-passe num único controlador de domínio foram efetuadas através de uma única hora. Por exemplo, este cenário poderá ocorrer quando muitos funcionários todas voltam a trabalhar na manhã de segunda-feira. Até mesmo nesse caso, estamos ainda a analisar ~333/60 minutos = seis alterações de palavra-passe por minuto, o que mais uma vez que não é uma carga significativa.

No entanto se os controladores de domínio atual já estão em execução em níveis de desempenho limitado (por exemplo, alcance o limite máximo em relação à CPU, espaço em disco, e/s de disco, etc.), é recomendado adicionar controladores de domínio adicionais ou expandir o espaço em disco disponível, antes de implementar esta funcionalidade. Consulte também a pergunta acima sobre a utilização de espaço de disco de sysvol acima.

**P: Pretendo proteção de palavra-passe do Azure AD em apenas alguns controladores de domínio de teste no meu domínio. É possível forçar as alterações de palavra-passe do utilizador para utilizar os controladores de domínio específicos?**

Não. O sistema operacional de cliente do Windows controla qual controlador de domínio é utilizado quando um usuário altera a palavra-passe. O controlador de domínio é selecionado com base em fatores como atribuições de sites e sub-rede do Active Directory, configuração de rede específicos do ambiente, etc. Proteção de palavra-passe do AD do Azure não controla estes fatores e não é possível influenciar que controlador de domínio está selecionada para alterar a palavra-passe de um utilizador.

Uma forma de parcialmente atingir esse objetivo seria implementar a proteção de palavra-passe do Azure AD em todos os controladores de domínio num determinado site do Active Directory. Essa abordagem irá fornecer cobertura razoável para os clientes do Windows que são atribuídos a esse site, e, portanto, também para os utilizadores que são início de sessão nesses clientes e alterar as palavras-passe.

**P: Se eu instalar o serviço de agente do Azure AD palavra-passe DC de proteção em apenas o domínio controlador primário (PDC), serão todos os outros controladores de domínio no domínio também protegidos?**

Não. Quando palavra-passe um utilizador é alterada num controlador de domínio não PDC determinado, a palavra-passe de texto não encriptado nunca é enviada para o PDC (essa idéia é uma percepção incorrecta comuns). Depois de uma nova palavra-passe é aceite num determinado DC, esse controlador de domínio utiliza essa palavra-passe para criar os hashes de específica de protocolo de autenticação vários dessa palavra-passe e, em seguida, persistir desses hashes no diretório. A palavra-passe de texto não encriptado não é persistente. Os hashes atualizados, em seguida, são replicados para o PDC. As palavras-passe do utilizador poderão, em alguns casos, ser alteradas diretamente no PDC, novamente consoante vários fatores como a topologia de rede e de design do site do Active Directory. (Consulte a pergunta anterior).

Em resumo, a implementação do serviço de agente do Azure AD palavra-passe DC de proteção no PDC é necessário para atingir 100% de cobertura de segurança do recurso no domínio. Implementar a funcionalidade em PDC apenas não fornece benefícios de segurança de proteção de palavra-passe do Azure AD para quaisquer outros controladores de domínio no domínio.

**P: Um pacote de gestão do System Center Operations Manager está disponível para a proteção de palavra-passe do Azure AD?**

Não.

## <a name="additional-content"></a>Conteúdo adicional

Os links a seguir não fazem parte da principal documentação da proteção de palavra-passe do Azure AD, mas podem ser uma fonte útil de obter mais informações sobre a funcionalidade.

[Guia de proteção de Phishing – de e-mail parte 15: Implementar o serviço de proteção de palavra-passe do Microsoft Azure AD (no local demasiado!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Bloqueio inteligente e a proteção de palavra-passe do AD do Azure estão agora em pré-visualização pública!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Treinamento de suporte Microsoft Premier\Unified disponível

Se estiver interessado em aprender mais sobre a proteção de palavra-passe do Azure AD e implantá-lo em seu ambiente, pode tirar partido de um serviço proativa da Microsoft disponível para esses clientes com um contrato de suporte Premier ou unificado. O serviço é chamado o Azure Active Directory: Proteção de palavra-passe. Para obter mais informações, contacte o seu gestor técnico de conta.

## <a name="next-steps"></a>Passos Seguintes

Se tiver uma questão de proteção de palavra-passe do Azure AD no local que não está respondida aqui, submeta um item de comentário abaixo - obrigado!

[Implementar proteção de palavras-passe do Azure AD](howto-password-ban-bad-on-premises-deploy.md)
