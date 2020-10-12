---
title: No local Azure AD Password Protection FAQ
description: Reveja perguntas frequentes para a proteção de senhas Azure AD num ambiente de Serviços de Domínio de Diretório Ativo no local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: a39871fd6e2aef2e5120030d17192bb32ba2613b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89003478"
---
# <a name="azure-ad-password-protection-on-premises-frequently-asked-questions"></a>Azure AD Password Protection no local frequentemente perguntas

Esta secção fornece respostas a muitas perguntas comumente feitas sobre a Proteção de Passwords AZure AD.

## <a name="general-questions"></a>Perguntas gerais

**P: Que orientação deve ser dada aos utilizadores sobre como selecionar uma palavra-passe segura?**

A orientação atual da Microsoft sobre este tópico pode ser encontrada no seguinte link:

[Orientação da palavra-passe da Microsoft](https://www.microsoft.com/research/publication/password-guidance)

**P: No local a Proteção de Passwords Azure AD é suportada em nuvens não públicas?**

Não - no local Azure AD Password Protection só é suportado na nuvem pública. Não foi anunciada qualquer data para a disponibilidade não pública de nuvem.

O portal AD AZure permite a modificação da configuração "Password protection for Windows Server Ative Directory" específica para o Windows Server, mesmo em nuvens não públicas; tais alterações serão persistiu, mas de outra forma nunca produzirão efeito. O registo de agentes ou florestas no local não é suportado quando são utilizadas credenciais de nuvem não pública, e quaisquer tentativas de registo serão sempre falhadas.

**P: Como posso aplicar benefícios de proteção de senha azure AD a um subconjunto dos meus utilizadores no local?**

Não suportado. Uma vez implementado e ativado, o Azure AD Password Protection não discrimina - todos os utilizadores recebem benefícios de segurança iguais.

**P: Qual é a diferença entre uma alteração de senha e um conjunto de palavra-passe (ou reset)?**

Uma mudança de palavra-passe é quando um utilizador escolhe uma nova palavra-passe depois de provar que tem conhecimento da senha antiga. Por exemplo, uma alteração de palavra-passe é o que acontece quando um utilizador entra no Windows e é solicitado a escolher uma nova palavra-passe.

Um conjunto de palavra-passe (por vezes chamado de redefinição de palavra-passe) é quando um administrador substitui a palavra-passe numa conta por uma nova palavra-passe, por exemplo, utilizando a ferramenta de gestão de Utilizadores de Diretório Ativo e Computadores. Esta operação requer um elevado nível de privilégio (geralmente Domain Admin), e a pessoa que executa a operação geralmente não tem conhecimento da senha antiga. Os cenários de help-desk muitas vezes executam conjuntos de palavras-passe, por exemplo, quando ajudam um utilizador que se esqueceu da sua palavra-passe. Também verá eventos de definição de palavra-passe quando uma nova conta de utilizador estiver a ser criada pela primeira vez com uma palavra-passe.

A política de validação de palavras-passe comporta-se da mesma forma, independentemente de estar a ser feita uma alteração ou um conjunto de palavras-passe. O serviço Azure AD Password Protection DC Agent regista eventos diferentes para informá-lo se foi feita uma alteração de senha ou uma operação de definição de palavras-passe.  Consulte [a monitorização e registo de registo de passwords Azure AD](./howto-password-ban-bad-on-premises-monitor.md).

**P: A Azure AD Password Protection valida as palavras-passe existentes após a instalação?**

Não - A Azure AD Password Protection só pode impor a política de palavra-passe em palavras-passe claras durante uma alteração de palavra-passe ou operação definida. Uma vez que uma palavra-passe é aceite pelo Ative Directory, apenas os hashes específicos do protocolo de autenticação dessa palavra-passe são persistidos. A palavra-passe de texto claro nunca é persistiu, pelo que a Proteção de PasswordS AD Azure não pode validar as palavras-passe existentes.

Após a implementação inicial da Azure AD Password Protection, todos os utilizadores e contas irão eventualmente começar a usar uma palavra-passe validada por Password AD Azure, uma vez que as suas palavras-passe existentes expiram normalmente ao longo do tempo. Se desejar, este processo pode ser acelerado por uma expiração manual única das palavras-passe da conta de utilizador.

As contas configuradas com "password nunca expira" nunca serão obrigadas a alterar a sua palavra-passe a menos que a expiração manual seja feita.

**P: Por que razão são registados eventos duplicados de rejeição de palavras-passe ao tentar definir uma palavra-passe fraca utilizando o snap-in de gestão de Diretório Ativo e computadores?**

O snap-in de gestão de utilizadores e computadores de Diretório Ativo tentará primeiro definir a nova palavra-passe utilizando o protocolo Kerberos. Após a falha, o snap-in fará uma segunda tentativa para definir a palavra-passe usando um protocolo legado (SAM RPC) (os protocolos específicos utilizados não são importantes). Se a nova palavra-passe for considerada fraca pela Azure AD Password Protection, este comportamento de snap-in resultará em dois conjuntos de eventos de rejeição de redefinição de palavra-passe que estão a ser registados.

**P: Porque é que os eventos de validação da palavra-passe Azure AD estão a ser registados com um nome de utilizador vazio?**

O Ative Directory suporta a capacidade de testar uma palavra-passe para ver se passa nos atuais requisitos de complexidade da palavra-passe do domínio, por exemplo, utilizando o [NetValidatePasswordPolicy](/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) api. Quando uma palavra-passe é validada desta forma, o teste também inclui validação por produtos baseados em password-filter-dll, como a Proteção de PasswordS AD Azure - mas os nomes de utilizador passados para um dado filtro de senha dll estarão vazios. Neste cenário, o Azure AD Password Protection ainda validará a palavra-passe utilizando a política de senha atualmente em vigor e emitirá uma mensagem de registo de eventos para capturar o resultado, no entanto a mensagem de registo de eventos terá campos de nome de utilizador vazios.

**P: É suportado para instalar a Proteção de PasswordS Azure AD lado a lado com outros produtos baseados em filtro de palavra-passe?**

Sim. O suporte para vários dlls de filtro de senha registados é uma funcionalidade core do Windows e não é específico para a Proteção de Passwords AD AZure. Todos os filtros de senha registados devem concordar antes de uma palavra-passe ser aceite.

**P: Como posso implementar e configurar a Proteção de Passwords Azure AD no meu ambiente de Diretório Ativo sem usar o Azure?**

Não suportado. A azure AD Password Protection é uma funcionalidade Azure que suporta ser estendida para um ambiente de Diretório Ativo no local.

**P: Como posso modificar o conteúdo da política ao nível do Ative Directory?**

Não suportado. A apólice só pode ser administrada utilizando o portal AZure AD. Consulte também a pergunta anterior.

**P: Por que é necessário DFSR para a replicação sysvol?**

O FRS (a tecnologia antecessora da DFSR) tem muitos problemas conhecidos e não é totalmente suportado em versões mais recentes do Windows Server Ative Directory. Os testes zero da Proteção de Senha AD Azure serão feitos em domínios configurados por FRS.

Para mais informações, consulte os seguintes artigos:

[O caso da replicação sysvol migratória para o DFSR](/archive/blogs/askds/the-case-for-migrating-sysvol-to-dfsr)

[O Fim é Nigh para FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

Se o seu domínio ainda não estiver a utilizar o DFSR, deve emigrá-lo para utilizar o DFSR antes de instalar a Proteção de Password AD Azure. Para mais informações, consulte o seguinte link:

[Guia de migração de replicação SYSVOL: FRS à replicação de DFS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> O software Azure AD Password Protection DC Agent irá atualmente instalar-se em controladores de domínio em domínios que ainda utilizam FRS para replicação sysvol, mas o software NÃO funcionará corretamente neste ambiente. Os efeitos colaterais negativos adicionais incluem ficheiros individuais que não conseguem replicar-se, e os procedimentos de restauro sysvol parecem ter sucesso, mas silenciosamente não conseguem replicar todos os ficheiros. Deve migrar o seu domínio para utilizar o DFSR o mais rapidamente possível, tanto para os benefícios inerentes da DFSR como também para desbloquear a implementação da Proteção de Password AD AD Azure. Futuras versões do software serão automaticamente desativadas quando estiverem a funcionar num domínio que ainda utiliza FRS.

**P: Quanto espaço em disco requer na partilha sysvol de domínio?**

O uso preciso do espaço varia, uma vez que depende de fatores como o número e a duração dos tokens proibidos na lista global de banidos da Microsoft e a lista personalizada por inquilino, além de encriptação por cima. É provável que o conteúdo destas listas cresça no futuro. Com isso em mente, uma expectativa razoável é que a funcionalidade precisará de pelo menos cinco (5) megabytes de espaço na partilha de domínios sysvol.

**P: Por que é necessário um reboot para instalar ou atualizar o software do agente DC?**

Este requisito é causado pelo comportamento do Core Windows.

**P: Existe alguma forma de configurar um agente de DC para usar um servidor de procuração específico?**

N.º Uma vez que o servidor proxy é apátrida, não é importante que servidor de procuração específico seja usado.

**P: Não há problema em implementar o serviço de proteção de senhas Azure AD, lado a lado com outros serviços, como o Azure AD Connect?**

Sim. O serviço Azure AD Password Protection Proxy e o Azure AD Connect nunca devem entrar em conflito diretamente entre si.

Infelizmente, foi encontrada uma incompatibilidade entre a versão do serviço Microsoft Azure AD Connect Agent Updater que é instalado pelo software Azure AD Password Protection Proxy e a versão do serviço que é instalado pelo software [Azure Ative Directory Application Proxy.](../manage-apps/application-proxy.md) Esta incompatibilidade pode resultar na incapacidade do serviço De Atualização do Agente para obter contacto com a Azure para atualizações de software. Não é aconselhável instalar o Proxy de Proteção de Passwords Azure AD e o Proxy de Aplicação de Diretório Ativo Azure na mesma máquina.

**P: Em que ordem devem os agentes e proxies de DC ser instalados e registados?**

Qualquer encomenda de instalação de agente proxy, instalação de agente DC, registo florestal e registo proxy é suportado.

**P: Devo preocupar-me com o desempenho atingido pelos meus controladores de domínio ao implementar esta funcionalidade?**

O serviço Azure AD Password Protection DC Agent não deve ter um impacto significativo no desempenho do controlador de domínio numa implementação saudável do Ative Directory existente.

Para a maioria das operações de alteração de passwords do Ative Directory são uma pequena proporção da carga de trabalho global em qualquer controlador de domínio. Como exemplo, imagine um domínio ative directory com 10000 contas de utilizador e uma política MaxPasswordAge definida para 30 dias. Em média, este domínio verá operações de mudança de senha de 10000/30=~333 por dia, o que é um número menor de operações para um único controlador de domínio. Considere um potencial pior cenário: suponha que as alterações de senha ~333 num único DC foram feitas durante uma hora. Por exemplo, este cenário pode ocorrer quando muitos funcionários vêm todos trabalhar numa manhã de segunda-feira. Mesmo nesse caso, ainda estamos a olhar para ~333/60 minutos = seis alterações de senha por minuto, o que mais uma vez não é uma carga significativa.

No entanto, se os seus controladores de domínio atuais já estiverem a funcionar a níveis limitados de desempenho (por exemplo, no limite do limite em relação ao CPU, espaço em disco, disco I/O, etc.), é aconselhável adicionar controladores de domínio adicionais ou expandir o espaço de disco disponível, antes de implementar esta funcionalidade. Consulte também a pergunta acima sobre a utilização do espaço do disco sysvol acima.

**P: Quero testar a Proteção de Passwords AZure AD em apenas alguns DCs no meu domínio. É possível forçar alterações na palavra-passe do utilizador para utilizar esses DCs específicos?**

N.º O sistema operativo do cliente Windows controla qual o controlador de domínio utilizado quando um utilizador altera a sua palavra-passe. O controlador de domínio é selecionado com base em fatores como o site ative directy e as atribuições de sub-redes, configuração de rede específica do ambiente, etc. O Azure AD Password Protection não controla estes fatores e não pode influenciar qual o controlador de domínio selecionado para alterar a palavra-passe de um utilizador.

Uma forma de alcançar parcialmente este objetivo seria implementar a Proteção de PasswordS Azure AD em todos os controladores de domínio num determinado site do Ative Directory. Esta abordagem proporcionará uma cobertura razoável para os clientes windows que são atribuídos a esse site, e, portanto, também para os utilizadores que estão a iniciar sessão nesses clientes e a alterar as suas palavras-passe.

**P: Se eu instalar o serviço de agente DC de proteção de passwords Azure AD em apenas o Controlador de Domínio Primário (PDC), todos os outros controladores de domínio no domínio também serão protegidos?**

N.º Quando a palavra-passe de um utilizador é alterada num dado controlador de domínio não PDC, a palavra-passe de texto claro nunca é enviada para o PDC (esta ideia é uma perceção errada comum). Uma vez que uma nova palavra-passe é aceite em um dado DC, que DC usa essa palavra-passe para criar os vários hashes específicos do protocolo de autenticação dessa palavra-passe e, em seguida, persiste esses haeques no diretório. A palavra-passe de texto claro não persiste. Os hashes atualizados são então replicados no PDC. As palavras-passe do utilizador podem, em alguns casos, ser alteradas diretamente no PDC, novamente dependendo de vários fatores, tais como topologia de rede e design de site de ative diretório. (Ver a pergunta anterior.)

Em resumo, a implementação do serviço de agente DC de proteção de passwords Azure AD no PDC é necessária para alcançar uma cobertura de segurança 100% da funcionalidade em todo o domínio. A implementação da funcionalidade no PDC apenas não fornece benefícios de segurança de proteção de senha azure AD para quaisquer outros DCs no domínio.

**P: Porque é que o bloqueio inteligente personalizado não funciona mesmo depois de os agentes serem instalados no meu ambiente de Ative Directory?**

O bloqueio inteligente personalizado só é suportado no Azure AD. As alterações às definições de bloqueio inteligente personalizadas no portal AD AZure não têm qualquer efeito no ambiente de Ative Directory no local, mesmo com os agentes instalados.

**P: Um pacote de gestão de gestão de operações do System Center Está disponível para proteção de senha AZure AD?**

N.º

**P: Porque é que a Azure AD continua a rejeitar senhas fracas, apesar de ter configurado a política para estar em modo de Auditoria?**

O modo de auditoria só é suportado no ambiente ative directy no local. O Azure AD está implicitamente sempre no modo "enforce" quando avalia as palavras-passe.

**P: Os meus utilizadores vêem a mensagem de erro tradicional do Windows quando uma palavra-passe é rejeitada pela Azure AD Password Protection. É possível personalizar esta mensagem de erro para que os utilizadores saibam o que realmente aconteceu?**

N.º A mensagem de erro vista pelos utilizadores quando uma palavra-passe é rejeitada por um controlador de domínio é controlada pela máquina do cliente e não pelo controlador de domínio. Este comportamento acontece se uma palavra-passe é rejeitada pelas políticas de senha de ative do Diretório por defeito ou por uma solução baseada em filtro de palavra-passe, como a Proteção de Passwords AD Azure.

## <a name="additional-content"></a>Conteúdo adicional

Os seguintes links não fazem parte da documentação de proteção de senha azure AD core, mas podem ser uma fonte útil de informações adicionais sobre a funcionalidade.

[A Azure AD Password Protection está disponível geralmente!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Guia de Proteção de Phishing por E-mail – Parte 15: Implementar o Serviço de Proteção de Passwords AD do Microsoft Azure (também para instalações)](http://kmartins.com/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[A azure AD Password Protection e Smart Lockout estão agora em visualização pública!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Microsoft Premier\Formação de suporte unificada disponível

Se estiver interessado em saber mais sobre a Proteção de Passwords AZure AD e implementá-la no seu ambiente, pode aproveitar um serviço proativo da Microsoft disponível para os clientes com um contrato de apoio Premier ou Unificado. O serviço chama-se Azure Ative Directory: Password Protection. Contacte o seu Gestor técnico de conta para mais informações.

## <a name="next-steps"></a>Passos seguintes

Se tiver uma questão de Proteção de Password Azure AD que não seja respondida aqui, envie um item de Feedback abaixo - obrigado!

[Implementar proteção de palavras-passe do Azure AD](howto-password-ban-bad-on-premises-deploy.md)