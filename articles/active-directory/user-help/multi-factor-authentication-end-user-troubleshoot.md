---
title: Problemas comuns com a autenticação de dois fatores de conta - Azure AD
description: Soluções para alguns dos problemas de verificação de dois fatores mais comuns e a sua conta de trabalho ou escola.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 09/01/2020
ms.author: curtand
ms.reviewer: kexia
ms.custom: contperf-fy21q1
ms.openlocfilehash: 0cb5e3463eda5d77bbbdefe223954105fd125c71
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98033987"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>Problemas comuns com a verificação de dois fatores e a conta escolar ou profissional

Existem alguns problemas comuns de verificação de dois fatores que parecem acontecer com mais frequência do que qualquer um de nós gostaria. Reunimos este artigo para descrever correções para os problemas mais comuns.

A sua organização Azure Ative Directory (Azure AD) pode ativar a verificação de dois fatores para a sua conta. Quando a verificação de dois fatores está acesa, o seu inserção na conta requer uma combinação dos seguintes dados:

- O seu nome de utilizador
- A sua palavra-passe
- Um dispositivo móvel ou telefone

A verificação de dois fatores é mais segura do que apenas uma palavra-passe, porque a verificação de dois fatores requer algo _que você sabe_ mais algo que você _tem_. Nenhum hacker tem o seu telemóvel físico.

>[!Important]
>Se for administrador, pode encontrar mais informações sobre como configurar e gerir o seu ambiente AD AZure na [documentação AD Azure](../index.yml).

Este conteúdo pode ajudá-lo com o seu trabalho ou conta escolar, que é a conta que lhe é fornecida pela sua organização (por exemplo, dritan@contoso.com ). Se estiver a ter problemas com a verificação de dois fatores numa conta pessoal da Microsoft, que é uma conta que criou para si (por exemplo, danielle@outlook.com ), consulte [a verificação de dois fatores dentro ou fora para a sua conta Microsoft.](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)

## <a name="i-dont-have-my-mobile-device-with-me"></a>Não tenho o meu telemóvel comigo.

Acontece. Deixaste o teu dispositivo móvel em casa, e agora não podes usar o telemóvel para verificar quem és. Talvez tenha adicionado anteriormente um método alternativo para iniciar sôm na sua conta, como por exemplo através do seu telefone de escritório. Em caso afirmativo, pode utilizar este método alternativo agora. Se nunca tiver adicionado um método de verificação alternativo, pode contactar o balcão de ajuda da sua organização para obter assistência.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Para iniciar sôms no seu trabalho ou conta escolar usando outro método de verificação

1. Inscreva-se na sua conta mas selecione o **Sinal de outra forma** na página de **verificação de dois fatores.**

    ![Alterar sinal no método de verificação](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    Se não vir o **Sinal de outra forma,** significa que não estabeleceu outros métodos de verificação. Terá de contactar o seu administrador para ajudar a entrar na sua conta.

2. Escolha o seu método de verificação alternativo e continue com o processo de verificação de dois fatores.

## <a name="i-cant-turn-two-factor-verification-off"></a>Não posso desligar a verificação de dois fatores.

- Se estiver a utilizar uma verificação de dois fatores com uma conta pessoal para um serviço Microsoft, por alain@outlook.com tipo, pode [ligar e desligar a funcionalidade](https://account.live.com/proofs/Manage).

- Se estiver a usar uma verificação de dois fatores com o seu trabalho ou conta escolar, provavelmente significa que a sua organização decidiu que deve usar esta funcionalidade de segurança adicional. Não há como desligá-lo individualmente.

Se não é possível desativar a verificação de dois fatores, também pode ser devido aos incumprimentos de segurança que foram aplicados a nível da organização. Para obter mais informações sobre incumprimentos de segurança, veja [quais são os incumprimentos de segurança?](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="my-device-was-lost-or-stolen"></a>O meu dispositivo foi perdido ou roubado.

Se perdeu ou roubou o seu dispositivo móvel, pode tomar qualquer uma das seguintes ações:

- Inscreva-se utilizando um método diferente.
- Peça ao balcão de ajuda da sua organização para limpar as suas configurações.

Recomendamos vivamente que informe a secretária de ajuda da sua organização se o seu telefone foi perdido ou roubado. O balcão de ajuda pode escrivaninhar as atualizações adequadas à sua conta. Depois de as suas definições estarem limpas, será solicitado que [se registe para verificação de dois fatores](multi-factor-authentication-end-user-first-time.md) na próxima vez que iniciar sedu.

## <a name="im-not-receiving-the-verification-code-sent-to-my-mobile-device"></a>Não estou a receber o código de verificação enviado para o meu dispositivo móvel.

Não receber o seu código de verificação é um problema comum. O problema está tipicamente relacionado com o seu dispositivo móvel e as suas definições. Aqui estão algumas ações que podes tentar.

Experimente isto | Informação de orientação
--------- | ------------
Utilize a aplicação autenticadora ou códigos de verificação da Microsoft | Está a receber mensagens de erro "Atingiu o nosso limite em chamadas de verificação" ou "Atingiu o nosso limite em códigos de verificação de texto" durante a sinscagem. <br/><br/>A Microsoft pode limitar as tentativas de autenticação repetidas que são efetuadas pelo mesmo utilizador num curto espaço de tempo. Esta limitação não se aplica ao Microsoft Authenticator ou código de verificação. Se tiver atingido estes limites, pode utilizar a App Autenticador, código de verificação ou tentar entrar novamente em alguns minutos. <br/><br/> Está a receber uma mensagem de erro "Desculpe, estamos a ter problemas em verificar a sua conta" durante a sing-in. <br/><br/> A Microsoft pode limitar ou bloquear tentativas de autenticação de voz ou SMS que sejam realizadas pelo mesmo utilizador, número de telefone ou organização devido ao elevado número de tentativas de autenticação de voz ou SMS falhadas. Se estiver a experimentar este erro, pode tentar outro método, como a App Autenticador ou o código de verificação, ou contactar o seu administrador para obter apoio.
Reinicie o seu dispositivo móvel | Às vezes, o seu dispositivo só precisa de ser atualizado. Quando reinicia o seu dispositivo, todos os processos e serviços de fundo terminam. O reinício também desliga os componentes do núcleo do seu dispositivo. Qualquer serviço ou componente é atualizado quando reinicia o seu dispositivo.
Verifique se as suas informações de segurança estão corretas | Certifique-se de que as informações do seu método de verificação de segurança são precisas, especialmente os seus números de telefone. Se colocar o número de telefone errado, todos os seus alertas irão para esse número incorreto. Felizmente, esse utilizador não poderá fazer nada com os alertas, mas também não o ajudará a entrar na sua conta. Para se certificar de que as suas informações estão corretas, consulte as instruções no artigo [de definição do método de verificação de dois fatores.](multi-factor-authentication-end-user-manage-settings.md)
Verifique se as suas notificações estão ligadas | Certifique-se de que o seu dispositivo móvel tem notificações ligadas. Certifique-se de que são permitidos os seguintes modos de notificação: <br/><br/> &bull; Chamadas telefónicas <br/> &bull; A sua aplicação de autenticação <br/> &bull; A sua aplicação de mensagens de texto <br/><br/> Certifique-se de que estes modos criam um alerta _visível_ no seu dispositivo.
Certifique-se de que tem um sinal de dispositivo e ligação à Internet | Certifique-se de que as suas chamadas telefónicas e mensagens de texto estão a chegar ao seu dispositivo móvel. Peça a um amigo que lhe ligue e envie uma mensagem de texto para se certificar de que recebe os dois. Se não receber a chamada ou texto, verifique primeiro se o seu dispositivo móvel está ligado. Se o seu dispositivo estiver ligado, mas ainda não estiver a receber a chamada ou a mensagem, provavelmente há um problema com a sua rede. Precisa de falar com o seu fornecedor. Se tiver frequentemente problemas relacionados com o sinal, recomendamos que instale e utilize a [aplicação Microsoft Authenticator](user-help-auth-app-download-install.md) no seu dispositivo móvel. A aplicação autenticadora pode gerar códigos de segurança aleatórios para iniciar sing-in, sem necessitar de qualquer sinal de telemóvel ou ligação à Internet.
Desligar Não perturbe | Certifique-se de que não ligou a função **Não perturbe** o seu dispositivo móvel. Quando esta funcionalidade é ligada, as notificações não são permitidas para alertá-lo no seu dispositivo móvel. Consulte o manual do seu dispositivo móvel para obter instruções sobre como desligar esta função.
Desbloquear números de telefone | Nos Estados Unidos, as chamadas de voz da Microsoft vêm dos seguintes números: +1 (866) 539 4191, +1 (855) 330 8653 e +1 (877) 668 6536.
Verifique as definições relacionadas com a bateria | Se definir a otimização da bateria para impedir que as aplicações menos utilizadas se mantenham ativas em segundo plano, o seu sistema de notificação foi provavelmente afetado. Tente desligar a otimização da bateria tanto para a sua app de autenticação como para a sua aplicação de mensagens. Então tente entrar na sua conta de novo.
Desativar aplicações de segurança de terceiros | Algumas aplicações de segurança telefónica bloqueiam mensagens de texto e chamadas telefónicas de chamadas desconhecidas irritantes. Uma aplicação de segurança pode impedir o seu telefone de receber o código de verificação. Tente desativar quaisquer aplicações de segurança de terceiros no seu telefone e, em seguida, solicite que seja enviado outro código de verificação.

## <a name="im-not-being-prompted-for-my-second-verification-information"></a>Não estou a ser solicitado para a minha segunda informação de verificação.

Inscreva-se na sua conta de trabalho ou escola utilizando o seu nome de utilizador e senha. Em seguida, deverá ser solicitado para obter informações adicionais de verificação de segurança. Se não for solicitado, talvez ainda não tenha preparado o seu dispositivo. O seu dispositivo móvel deve ser configurado para funcionar com o seu método específico de verificação de segurança adicional.

Talvez ainda não tenhas preparado o teu dispositivo. O seu dispositivo móvel tem de ser configurado para funcionar com o seu método específico de verificação de segurança adicional. Para que os passos tornem o seu dispositivo móvel disponível para utilizar com o seu método de verificação, consulte [Gerir as definições do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md). Se sabe que ainda não montou o seu dispositivo ou a sua conta, pode seguir os passos na minha conta para o artigo de verificação em [duas etapas.](multi-factor-authentication-end-user-first-time.md)

## <a name="i-have-a-new-phone-number-and-i-want-to-add-it"></a>Tenho um novo número de telefone e quero adicioná-lo.

Se tiver um novo número de telefone, terá de atualizar os detalhes do seu método de verificação de segurança. Isto permite que as suas solicitações de verificação vão para o local certo. Para atualizar o seu método de verificação, siga os passos na secção **Adicionar ou altere a** secção de números de telefone do artigo de [definição de dois fatores.](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number)

## <a name="i-have-a-new-mobile-device-and-i-want-to-add-it"></a>Tenho um novo dispositivo móvel e quero adicioná-lo

Se tiver um novo dispositivo móvel, terá de o configurar para funcionar com uma verificação de dois fatores. Esta é uma solução em várias etapas:

1. Crie o seu dispositivo para trabalhar com a sua conta seguindo os passos na conta Configurar a minha conta para artigo de verificação em [duas etapas.](multi-factor-authentication-end-user-first-time.md)

1. Atualize as informações da sua conta e dispositivo na página **adicional de verificação de segurança.** Execute a atualização eliminando o seu dispositivo antigo e adicionando o seu novo. Para obter mais informações, consulte o artigo [de definição do método de verificação de dois fatores.](multi-factor-authentication-end-user-manage-settings.md)

Passos opcionais:

- Instale a aplicação Microsoft Authenticator no seu dispositivo móvel seguindo os passos no Download e instale o artigo [da aplicação Microsoft Authenticator.](user-help-auth-app-download-install.md)

- Ligue a verificação de dois fatores para os seus dispositivos fidedignos seguindo os passos na **verificação de dois fatores numa** secção de dispositivo fidedigno do artigo [de definição do método de verificação de dois fatores.](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Estou com problemas em iniciar sessão no meu dispositivo móvel enquanto viajava.

Pode ser mais difícil utilizar um método de verificação relacionado com dispositivos móveis, como uma mensagem de texto, enquanto estiver num local internacional. Também é possível que o seu dispositivo móvel possa causar incorrer em taxas de roaming. Para esta situação, recomendamos que utilize a aplicação Microsoft Authenticator, com a opção de ligar a um Wi-Fi ponto quente. Para obter mais informações sobre como configurar a aplicação Microsoft Authenticator no seu dispositivo móvel, consulte o Download e instale o artigo [da aplicação Microsoft Authenticator.](user-help-auth-app-download-install.md)

## <a name="i-cant-get-my-app-passwords-to-work"></a>Não consigo que as minhas palavras-passe funcionem.

As palavras-passe da aplicação substituem a sua palavra-passe normal por aplicações de ambiente de trabalho mais antigas que não suportam a verificação de dois fatores. Primeiro, certifique-se de escrever a senha corretamente. Se isso não o corrigir, tente criar uma nova senha de aplicação para a aplicação. Faça-o seguindo os passos na **Configuração e elimine as palavras-passe da aplicação utilizando a** secção portal My Apps das palavras-passe da aplicação Manage para artigo de verificação em [duas etapas.](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page)

## <a name="i-cant-turn-off-two-factor-verification"></a>Não posso desligar a verificação de dois fatores.

Se estiver a utilizar uma verificação de dois fatores com o seu trabalho ou conta escolar (por exemplo, alain@contoso.com ), o mais provável é que a sua organização tenha decidido usar esta funcionalidade de segurança adicional. Como a sua organização decidiu que deve usar esta funcionalidade, não há como desligá-la individualmente. Se, no entanto, estiver a utilizar a verificação de dois fatores com uma conta pessoal, por alain@outlook.com assim dizer, tem a capacidade de ligar e desligar a funcionalidade. Para obter instruções sobre como controlar a verificação de dois fatores para as suas contas pessoais, consulte [a verificação de dois fatores na sua conta Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

Se não é possível desativar a verificação de dois fatores, também pode ser devido aos incumprimentos de segurança que foram aplicados a nível da organização. Para obter mais informações sobre incumprimentos de segurança, veja [quais são os incumprimentos de segurança?](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Não encontrei uma resposta para o meu problema.

Se já experimentou estes passos, mas ainda está com problemas, contacte o balcão de ajuda da sua organização para obter ajuda.

## <a name="related-articles"></a>Artigos relacionados

- [Gerir as definições do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md)

- [Configurar a minha conta para verificação em duas etapas](multi-factor-authentication-end-user-first-time.md)

- [FAQ da aplicação Microsoft Authenticator](user-help-auth-app-faq.md)
