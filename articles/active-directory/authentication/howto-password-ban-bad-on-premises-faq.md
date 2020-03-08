---
title: No local, azure AD Password Protection FAQ
description: A revisão frequentemente feita perguntas para a Proteção de Passwords AD Azure em um ambiente de Serviços de Domínio de Diretório Ativo no local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: abef6e52e37cfa2faeb426bc59bb0de5a52a6658
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671671"
---
# <a name="azure-ad-password-protection-on-premises-frequently-asked-questions"></a>Proteção de senhas azure AD no local frequentemente perguntas

Esta secção fornece respostas a muitas perguntas comumente feitas sobre a Proteção de Passwords Azure AD.

## <a name="general-questions"></a>Perguntas gerais

**P: Que orientações devem ser dadas aos utilizadores sobre como selecionar uma palavra-passe segura?**

A orientação atual da Microsoft sobre este tópico pode ser encontrada no seguinte link:

[Orientação da palavra-passe da Microsoft](https://www.microsoft.com/research/publication/password-guidance)

**P: No local, a Proteção de Senhas Azure AD é suportada em nuvens não públicas?**

Não - no local a Proteção de Senhas Azure AD só é suportada na nuvem pública. Não foi anunciada qualquer data para a disponibilidade de nuvens não públicas.

O portal Azure AD permite a modificação da configuração "Password protection for Windows Server Ative Directory" específica para o Windows Server Ative Directory, mesmo em nuvens não públicas; tais alterações serão persistidas, mas de outra forma nunca produzirá efeito. O registo de agentes de procuração ou florestas no local não é suportado quando são utilizadas credenciais não públicas de nuvem, e tais tentativas de registo falharão sempre.

**P: Como posso aplicar benefícios de proteção de senhas Azure AD a um subconjunto dos meus utilizadores no local?**

Não suportado. Uma vez implementado e ativado, a Proteção de Passwords Azure AD não discrimina - todos os utilizadores recebem benefícios de segurança iguais.

**P: Qual é a diferença entre uma alteração de palavra-passe e um conjunto de palavra-passe (ou reset)?**

Uma alteração de palavra-passe é quando um utilizador escolhe uma nova senha depois de provar que tem conhecimento da antiga senha. Por exemplo, uma alteração de palavra-passe é o que acontece quando um utilizador inicia o login no Windows e é depois solicitado a escolher uma nova senha.

Um conjunto de palavras-passe (por vezes chamado de reset de palavra-passe) é quando um administrador substitui a palavra-passe numa conta por uma nova palavra-passe, por exemplo, utilizando a ferramenta de gestão de Utilizadores de Diretório ativo e Computadores. Esta operação requer um alto nível de privilégio (geralmente Administração de Domínio), e a pessoa que realiza a operação geralmente não tem conhecimento da antiga senha. Os cenários de help-desk realizam frequentemente conjuntos de palavras-passe, por exemplo, quando ajudam um utilizador que se esqueceu da sua palavra-passe. Também verá eventos de definição de palavra-passe quando uma nova conta de utilizador está a ser criada pela primeira vez com uma palavra-passe.

A política de validação de palavras-passe comporta-se da mesma forma, independentemente de estar a ser feita uma alteração ou um conjunto de palavras-passe. O serviço de proteção de senhas Azure AD DC agente regista diferentes eventos para informá-lo se foi feita uma alteração de senha ou uma operação definida.  Consulte a monitorização e o registo de [proteção de passwords AD Azure.](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor)

**P: Por que é que os eventos duplicados de rejeição de palavras-passe são registados ao tentar definir uma palavra-passe fraca usando o snap-in de gestão de utilizadores de diretório ativo e computadores?**

O snap-in de gestão de Utilizadores de Diretório Ativo e Computadores tentará primeiro definir a nova palavra-passe utilizando o protocolo Kerberos. Após a falha, o snap-in fará uma segunda tentativa de definir a palavra-passe usando um protocolo legado (SAM RPC) (os protocolos específicos utilizados não são importantes). Se a nova palavra-passe for considerada fraca pela Proteção de Passwords AD do Azure, este comportamento instantâneo resultará em dois conjuntos de eventos de rejeição de reset de palavra-passe a serem registados.

**P: Porque é que os eventos de validação de passwords de proteção de senhas da Azure AD estão a ser registados com um nome de utilizador vazio?**

O Ative Directy suporta a capacidade de testar uma palavra-passe para ver se passa nos atuais requisitos de complexidade da palavra-passe do domínio, por exemplo, utilizando o [api NetValidatePasswordPolicy.](https://docs.microsoft.com/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) Quando uma palavra-passe é validada desta forma, os testes também incluem validação por produtos baseados em password-filter-dll, como a Proteção de Passwords AD Azure - mas os nomes de utilizador passados para um determinado filtro de senha ficarão vazios. Neste cenário, a Proteção de Palavras-passe Da Azure AD ainda validará a palavra-passe utilizando a política de senha seletiva atualmente em vigor e emitirá uma mensagem de registo de eventos para capturar o resultado, no entanto a mensagem de registo de eventos terá campos de nome de utilizador vazios.

**P: É suportado para instalar a Proteção de Passwords Azure AD lado a lado com outros produtos baseados em filtros de palavra-passe?**

Sim. O suporte para vários dlls de filtro de senha registados é uma funcionalidade principal do Windows e não específica para a Proteção de Passwords AD Azure. Todos os dlls de filtro de senha registados devem concordar antes de uma palavra-passe ser aceite.

**P: Como posso implementar e configurar a Proteção de Passwords Azure AD no meu ambiente de Diretório Ativo sem utilizar o Azure?**

Não suportado. A Azure AD Password Protection é uma funcionalidade Azure que suporta ser estendida para um ambiente de Diretório Ativo no local.

**P: Como posso modificar o conteúdo da política ao nível do Diretório Ativo?**

Não suportado. A política só pode ser administrada através do portal Azure AD. Consulte também a pergunta anterior.

**P: Por que é necessário dfSR para replicação sysvol?**

FrS (a tecnologia antecessora para a DFSR) tem muitos problemas conhecidos e não suporta totalmente em versões mais recentes do Windows Server Ative Directory. Os testes zero da Proteção de Palavras-passe Azure AD serão feitos em domínios configurados por FRS.

Para mais informações, consulte os seguintes artigos:

[O caso da replicação sysvol migratória para a DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[The End is Nigh for FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

Se o seu domínio ainda não estiver a utilizar o DFSR, deve migrar para utilizar o DFSR antes de instalar a Proteção de Passwords Azure AD. Para mais informações, consulte o seguinte link:

[Guia de migração de replicação SYSVOL: FRS para replicação DFS](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> O software Azure AD Password Protection DC Agent irá atualmente instalar em controladores de domínio em domínios que ainda estão a usar FRS para replicação sysvol, mas o software NÃO funcionará corretamente neste ambiente. Efeitos colaterais negativos adicionais incluem ficheiros individuais que não se replicam, e os procedimentos de restauro sysvol parecem ter sucesso, mas falhando silenciosamente em replicar todos os ficheiros. Deve migrar o seu domínio para utilizar o DFSR o mais rapidamente possível, tanto para os benefícios inerentes da DFSR como para desbloquear a implementação da Proteção de Passwords Azure AD. As futuras versões do software serão automaticamente desativadas quando estiverem a funcionar num domínio que ainda está a utilizar frs.

**P: Quanto espaço em disco a funcionalidade requer na partilha de sysvol de domínio?**

O uso preciso do espaço varia uma vez que depende de fatores como o número e o comprimento dos tokens banidos na lista global proibida da Microsoft e a lista personalizada por inquilino, além de encriptação. É provável que o conteúdo destas listas cresça no futuro. Com isso em mente, uma expectativa razoável é que a funcionalidade precisará de pelo menos cinco (5) megabytes de espaço na partilha de sysvol de domínio.

**P: Por que é necessário um reboot para instalar ou atualizar o software do agente DC?**

Este requisito é causado pelo comportamento do Núcleo do Windows.

**P: Existe alguma maneira de configurar um agente de DC para usar um servidor de procuração específico?**

Não. Uma vez que o servidor proxy é apátrida, não é importante qual servidor de procuração específico é usado.

**P: Não há problema em implementar o serviço de proxy de proteção de senhas Azure AD lado a lado com outros serviços, como o Azure AD Connect?**

Sim. O serviço de proxy de proteção de senhas Azure AD e azure AD Connect nunca devem entrar em conflito diretamente entre si.

Infelizmente, foi encontrada uma incompatibilidade entre a versão do serviço de atualização do Agente de AD Connect Da Microsoft Azure que é instalado pelo software Deproxy de Proteção de Passwords Azure AD e a versão do serviço que é instalado pelo software [Proxy de Aplicação de Aplicação de Diretório Ativo Do Azure.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) Esta incompatibilidade pode resultar na incapacidade do serviço de Atualização de Agente para obter atualizações de software com a Azure. Não é aconselhável instalar o Proxy de Proteção de Passwords Azure AD e o Proxy de Aplicação de Diretório Ativo Azure na mesma máquina.

**P: Em que ordem devem ser instalados e registados os agentes e proxies de DC?**

Qualquer ordenação da instalação do agente Proxy, instalação de agente dc, registo florestal e registo proxy é suportado.

**P: Devo preocupar-me com o desempenho dos meus controladores de domínio de implementar esta funcionalidade?**

O serviço de agente DC de proteção de senhas Azure AD não deve ter um impacto significativo no desempenho do controlador de domínio numa implementação saudável do Diretório Ativo.

Para a maioria das operações de alteração de palavras-passe do Diretório Ativo, uma pequena proporção da carga de trabalho global em qualquer controlador de domínio. Como exemplo, imagine um domínio de Diretório Ativo com 10000 contas de utilizador e uma política MaxPasswordAge definida para 30 dias. Em média, este domínio verá operações de alteração de senha 10000/30=~~333 por dia, o que é um número menor de operações para até mesmo um único controlador de domínio. Considere um potencial pior cenário: suponha que as alterações de senha de ~333 num único DC foram feitas ao longo de uma hora. Por exemplo, este cenário pode ocorrer quando muitos funcionários vêm trabalhar numa manhã de segunda-feira. Mesmo nesse caso, ainda estamos a olhar para ~333/60 minutos = seis alterações de senha por minuto, o que novamente não é uma carga significativa.

No entanto, se os seus controladores de domínio atual já estiverem a funcionar a níveis limitados de desempenho (por exemplo, no limite em relação ao CPU, espaço do disco, disco I/O, etc.), é aconselhável adicionar controladores de domínio adicionais ou expandir o espaço disponível do disco, antes de implementar esta funcionalidade. Consulte também a questão acima sobre o uso do espaço do disco sysvol acima.

**P: Quero testar a Proteção de Senhas Azure AD em apenas alguns DCs no meu domínio. É possível forçar alterações de palavra-passe do utilizador a utilizar esses DCs específicos?**

Não. O SISTEMA operativo cliente Windows controla o controlador de domínio utilizado quando um utilizador altera a sua palavra-passe. O controlador de domínio é selecionado com base em fatores como o site ative directory e atribuições de sub-redes, configuração de rede específica para o ambiente, etc. A Proteção de Palavras-passe Azure AD não controla estes fatores e não pode influenciar qual o controlador de domínio selecionado para alterar a palavra-passe de um utilizador.

Uma forma de atingir parcialmente este objetivo seria implementar a Proteção de Passwords Azure AD em todos os controladores de domínio num determinado site de Diretório Ativo. Esta abordagem proporcionará uma cobertura razoável para os clientes do Windows que estão atribuídos a esse site, e, portanto, também para os utilizadores que estão a aceder a esses clientes e a alterar as suas palavras-passe.

**P: Se eu instalar o serviço de agente DC de proteção de senhas Azure AD apenas no Controlador de Domínio Primário (PDC), todos os outros controladores de domínio no domínio também serão protegidos?**

Não. Quando a palavra-passe de um utilizador é alterada num determinado controlador de domínio não PDC, a palavra-passe de texto claro nunca é enviada para o PDC (esta ideia é uma perceção errada comum). Uma vez que uma nova senha é aceite em um dado DC, que DC usa essa palavra-passe para criar as várias hashes específicas do protocolo de autenticação dessa palavra-passe e, em seguida, persiste essas hashes no diretório. A palavra-passe de texto claro não é persistiu. Os hashes atualizados são então replicados para o PDC. As palavras-passe dos utilizadores podem, em alguns casos, ser alteradas diretamente no PDC, novamente dependendo de vários fatores, como a topologia da rede e o design do site Ative Directory. (Ver a pergunta anterior.)

Em resumo, a implementação do serviço de agente DC de proteção de senhas Azure AD no PDC é necessária para alcançar uma cobertura de segurança 100% da funcionalidade em todo o domínio. A implementação da funcionalidade no PDC apenas não fornece benefícios de proteção de senhas Azure AD para quaisquer outros DCs no domínio.

**P: Porque é que o bloqueio inteligente personalizado não funciona mesmo depois de os agentes estarem instalados no meu ambiente de Diretório Ativo no local?**

O bloqueio inteligente personalizado só é suportado em Azure AD. As alterações às definições de bloqueio inteligente personalizadas no portal Azure AD não têm qualquer efeito no ambiente ative diretório no local, mesmo com os agentes instalados.

**P: Um pacote de gestão de gestão de gestão de operações do System Center está disponível para proteção de passwords AD Azure?**

Não.

**P: Porque é que o Azure AD continua a rejeitar senhas fracas, apesar de eu ter configurado a política para estar em modo de Auditoria?**

O modo de auditoria só é suportado no ambiente de Diretório Ativo no local. O Azure AD está implicitamente sempre em modo de "impor" quando avalia as palavras-passe.

**P: Os meus utilizadores vêem a mensagem de erro tradicional do Windows quando uma palavra-passe é rejeitada pela Proteção de Passwords AD do Azure. É possível personalizar esta mensagem de erro para que os utilizadores saibam o que realmente aconteceu?**

Não. A mensagem de erro vista pelos utilizadores quando uma palavra-passe é rejeitada por um controlador de domínio é controlada pela máquina do cliente e não pelo controlador de domínio. Este comportamento acontece se uma palavra-passe é rejeitada pelas políticas de senha de diretório ativo padrão ou por uma solução baseada em filtro de senha, como a Proteção de Passwords Azure AD.

## <a name="additional-content"></a>Conteúdo adicional

Os seguintes links não fazem parte da documentação de proteção de senhas azure ad central, mas podem ser uma fonte útil de informações adicionais sobre a funcionalidade.

[A Proteção de Passwords Azure AD já está geralmente disponível!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Guia de proteção de phishing por e-mail – Parte 15: Implementar o Serviço de Proteção de Passwords AD da Microsoft Azure (também para as instalações!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[A Proteção de Passwords Azure AD e o Smart Lockout estão agora em Pré-visualização pública!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Microsoft Premier\Unified support training disponível

Se estiver interessado em saber mais sobre a Proteção de Passwords Azure AD e implementá-la no seu ambiente, pode aproveitar um serviço proactivo da Microsoft disponível para os clientes com um contrato de suporte Premier ou Unificado. O serviço chama-se Diretório Ativo Azure: Proteção de Passwords. Contacte o seu Gestor de Conta Técnica para obter mais informações.

## <a name="next-steps"></a>Passos seguintes

Se tiver uma pergunta de proteção de senhas Azure AD que não seja respondida aqui, submeta um item de Feedback abaixo - obrigado!

[Implementar proteção de palavras-passe do Azure AD](howto-password-ban-bad-on-premises-deploy.md)
