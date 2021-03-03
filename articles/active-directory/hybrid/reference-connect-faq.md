---
title: Azure Ative Directory Connect FAQ - | Microsoft Docs
description: Este artigo responde a perguntas frequentes sobre Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29c0ae8ec210356f6027a46ed01f2a7126ea4a49
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644736"
---
# <a name="azure-active-directory-connect-faq"></a>Diretório Ativo Azure Conecta FAQ

## <a name="general-installation"></a>Instalação geral

**P: Como posso endurecer o meu servidor Azure AD Connect para diminuir a superfície de ataque de segurança?**

A Microsoft recomenda endurecer o seu servidor Azure AD Connect para diminuir a superfície de ataque de segurança para este componente crítico do seu ambiente de TI.  Seguindo as recomendações abaixo diminuirá os riscos de segurança para a sua organização.

* Implementar Azure AD Connect num servidor de união de domínios e restringir o acesso administrativo a administradores de domínios ou a outros grupos de segurança rigorosamente controlados

Para saber mais, veja: 

* [Assegurar grupos de administradores](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Assegurar contas de administrador incorporadas](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Melhoria e sustentação da segurança reduzindo as superfícies de ataque](/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Redução da superfície de ataque do Ative Directory](/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

**P: Funcionará a instalação se o Azure Ative Directory (Azure AD) Global Admin tiver uma autenticação de dois fatores (2FA) ativada?**  
A partir das construções de fevereiro de 2016, este cenário é apoiado.

**P: Existe uma maneira de instalar o Azure AD Connect sem vigilância?**  
A instalação Azure AD Connect só é suportada quando utilizar o assistente de instalação. Uma instalação silenciosa e sem vigilância não é suportada.

**P: Eu tenho uma floresta onde um domínio não pode ser contactado. Como posso instalar o Azure AD Connect?**  
A partir das construções de fevereiro de 2016, este cenário é apoiado.

**P: O agente de saúde Azure Ative Directory Domain Services (Azure AD DS) funciona no núcleo do servidor?**  
Sim. Depois de instalar o agente, pode concluir o processo de registo utilizando o seguinte cmdlet PowerShell: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**P: O Azure AD Connect suporta sincronizar de dois domínios para um AD Azure?**  
Sim, este cenário é apoiado. Consulte [vários domínios](how-to-connect-install-multiple-domains.md).
 
**P: Pode ter vários conectores para o mesmo domínio ative directory em Azure AD Connect?**  
Não, vários conectores para o mesmo domínio AD não são suportados. 

**P: Posso mover a base de dados AZure AD Connect da base de dados local para uma instância remota do SQL Server?**   
Sim, os seguintes passos fornecem orientação geral sobre como fazê-lo. Estamos neste momento a trabalhar num documento mais detalhado.
1. Volte a subir a base de dados localDB ADSync.
A forma mais simples de o fazer é utilizar o SQL Server Management Studio instalado na mesma máquina que o Azure AD Connect. Ligue a *(LocalDb).\ADSync*, e, em seguida, re-up na base de dados ADSync.

2. Restaurar a base de dados ADSync para a sua instância remota do SQL Server.

3. Instale o Azure AD Connect com a base de [dados SQL remota](how-to-connect-install-existing-database.md)existente.
   O artigo demonstra como migrar para a utilização de uma base de dados SQL local. Se estiver a migrar para utilizar uma base de dados SQL remota, no passo 5 do processo também deve introduzir uma conta de serviço existente que o serviço Windows Sync irá funcionar como. Esta conta de serviço do motor de sincronização é descrita aqui:
   
      **Utilize uma conta de serviço existente**: Por padrão, o Azure AD Connect utiliza uma conta de serviço virtual para os serviços de sincronização utilizarem. Se utilizar uma instância remota do SQL Server ou utilizar um representante que exija autenticação, utilize uma conta de serviço gerida ou uma conta de serviço no domínio e conheça a palavra-passe. Nesses casos, introduza a conta a utilizar. Certifique-se de que os utilizadores que estão a executar a instalação são administradores de sistema em SQL para que possam ser criadas credenciais de login para a conta de serviço. Para mais informações, consulte [as contas e permissões do Azure AD Connect](reference-connect-accounts-permissions.md#adsync-service-account). 
   
      Com a compilação mais recente, o aprovisionamento da base de dados pode agora ser realizado fora de banda pelo administrador SQL e, em seguida, instalado pelo administrador do Azure AD Connect com direitos de proprietário da base de dados. Para obter mais informações, consulte [instalar o Azure AD Connect utilizando permissões de administrador delegadas sql](how-to-connect-install-sql-delegation.md).

Para manter as coisas simples, recomendamos que os utilizadores que instalam o Azure AD Connect sejam administradores de sistemas em SQL. No entanto, com construções recentes, pode agora utilizar administradores SQL delegados, conforme descrito no [Install Azure AD Connect utilizando permissões de administrador delegados sql](how-to-connect-install-sql-delegation.md).

**P: Quais são algumas das melhores práticas do campo?**  

Segue-se um documento informativo que apresenta algumas das melhores práticas que a engenharia, o apoio e os nossos consultores têm desenvolvido ao longo dos anos.  Isto é apresentado numa lista de balas que pode ser rapidamente referenciada.  Embora esta lista tente ser abrangente, pode haver outras boas práticas que podem ainda não ter chegado à lista.

- Se utilizar o SQL Completo, então deve permanecer local vs. remoto
    - Menos lúpulo
    - Mais fácil de resolver problemas
    - Menos complexidade
    - Precisa de designar recursos para o SQL e permitir sobrecargas para Azure AD Connect e OS
- Bypass Proxy se possível, se não conseguir contornar o representante, então tem de garantir que o valor do tempo limite é superior a 5 minutos.
- Se for necessário um representante, então deve adicionar o representante ao ficheiro machine.config
- Esteja atento aos empregos e manutenção locais do SQL e como terão impacto no Azure AD Connect - particularmente na re-indexação
- Certifique-se de que o DNS pode resolver externamente
- Certifique-se de que [as especificações do servidor](how-to-connect-install-prerequisites.md#hardware-requirements-for-azure-ad-connect) são por recomendação, quer esteja a utilizar servidores físicos ou virtuais
- Certifique-se de que se estiver a usar um servidor virtual que os recursos necessários são dedicados
- Certifique-se de que tem a configuração do disco e do disco para cumprir as melhores práticas para o SQL Server
- Instale e configuize a Azure AD Connect Health para monitorização
- Utilize o Limiar de Eliminação que é incorporado no Azure AD Connect.
- Reveja cuidadosamente as atualizações de lançamento a preparar para todas as alterações e novos atributos que podem ser adicionados
- Backup de tudo
    - Chaves de reserva
    - Regras de Sincronização de Backup
    - Configuração do servidor de backup
    - Base de Dados SQL de backup
- Certifique-se de que não existem agentes de backup de terceiros que estejam a apoiar o SQL sem o SQL VSS Writer (comum em servidores virtuais com fotos de terceiros)
- Limitar a quantidade de regras de sincronização personalizadas que são usadas à medida que adicionam complexidade
- Trate os servidores de ligação Azure Ad como servidores tier 0
- Seja sota-vento de modificar as regras de sincronização em nuvem sem grande compreensão do impacto e dos motoristas de negócios certos
- Certifique-se de que as portas de URL e Firewall corretas estão abertas para suporte de Azure AD Connect e Azure AD Connect Health
- Aproveite o atributo filtrado pela nuvem para resolver problemas e evite objetos fantasma
- Com o Servidor de Realização certifique-se de que está a utilizar o Documento de Configuração de Ligação AD Azure para obter consistência entre servidores
- Os Servidores de Encenação devem estar em centros de dados separados (Localizações Físicas
- Os servidores de staging não são para ser uma solução de Alta Disponibilidade, mas você pode ter vários servidores de paragem
- Introduzir um "Lag" Staging Servers pode atenuar algum potencial tempo de inatividade em caso de erro
- Teste e validar todas as atualizações no Servidor de Encenação primeiro
- Valide sempre as exportações antes de passar para o servidor de preparação.  Aproveite o servidor de staging para importações completas e sincronizações completas para reduzir o impacto do negócio
- Mantenha a consistência da versão entre os Servidores AD AD do Azure tanto quanto possível 

**P: Posso permitir que o Azure AD Connect crie a conta Azure AD Connector na máquina do Grupo de Trabalho?**
N.º  Para permitir que o Azure AD Connect crie automaticamente a conta Azure AD Connector, a máquina deve ser ligada ao domínio.  

## <a name="network"></a>Rede
**P: Tenho uma firewall, dispositivo de rede, ou outra coisa que limita o tempo que as ligações podem permanecer abertas na minha rede. Qual deve ser o limite de tempo do lado do cliente quando eu usar O AZure AD Connect?**  
Todos os softwares de rede, dispositivos físicos ou qualquer outra coisa que limite o tempo máximo que as ligações podem permanecer abertas devem utilizar um limiar de pelo menos cinco minutos (300 segundos) para a conectividade entre o servidor onde o cliente Azure AD Connect está instalado e o Azure Ative Directory. Esta recomendação aplica-se também a todas as ferramentas de sincronização do Microsoft Identity previamente lançadas.

**P: Os domínios de rótulo único (SLDs) são suportados?**  
Embora recomendemos fortemente contra esta configuração de rede[(ver artigo),](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains)a sincronização Azure AD Connect com um único domínio de etiqueta é suportada, desde que a configuração da rede para o domínio de um único nível esteja a funcionar corretamente.

**P: As florestas com domínios deD desarticulado são suportadas?**  
Não, o Azure AD Connect não suporta florestas no local que contenham espaços de nome desarticulado.

**P: Os nomes "pontilhados" do NetBIOS são suportados?**  
Não, o Azure AD Connect não suporta florestas ou domínios no local onde o nome NetBIOS contenha um ponto (.).

**P: O ambiente IPv6 puro é suportado?**  
Não, o Azure AD Connect não suporta um ambiente IPv6 puro.

**P: I ter um ambiente multi-florestal e a rede entre as duas florestas está a utilizar o NAT (Network Address Translation). A utilização do Azure AD Connect entre estas duas florestas está suportada?**</br>
Não, a utilização do Azure AD Connect sobre o NAT não é suportada. 

## <a name="federation"></a>de Federação
**P: O que faço se receber um e-mail que me pede para renovar o meu certificado Microsoft 365?**  
Para obter orientações sobre a renovação do certificado, consulte [os certificados de renovação.](how-to-connect-fed-o365-certs.md)

**P: Tenho "Atualização automática da festa de confiante" definida para a festa de apoio da Microsoft 365. Tenho de tomar alguma ação quando o meu certificado de assinatura de fichas se passa automaticamente?**  
Utilize as orientações descritas no artigo [renovar certificados.](how-to-connect-fed-o365-certs.md)

## <a name="environment"></a>Ambiente
**P: É suportado para renomear o servidor depois de Azure AD Connect ter sido instalado?**  
N.º A alteração do nome do servidor torna o motor de sincronização incapaz de se ligar à instância da base de dados SQL e o serviço não pode ser iniciado.

**P: As regras de sincronização cryptográficas de próxima geração (NGC) são suportadas numa máquina ativada por FIPS?**  
N.º  Não são suportadas.

**Q. Se desativei um dispositivo sincronizado (por exemplo: HAADJ) no portal Azure, por que é reativado?**<br>
Os dispositivos sincronizados podem ser da autoria ou dominados nas instalações. Se um dispositivo sincronizado estiver ativado no local, poderá ser reativado no portal Azure, mesmo que tenha sido previamente desativado por um administrador. Para desativar um dispositivo sincronizado, utilize o Ative Directory no local para desativar a conta do computador.

**Q. Se bloquear o sessão de utilizadores no portal Microsoft 365 ou AZure AD para utilizadores sincronizados, por que razão é desbloqueado ao iniciar novamente a sessão?**<br>
Os utilizadores sincronizados podem ser da autoria ou dominados no local. Se a conta estiver ativada nas instalações, pode desbloquear o bloco de inscrição colocado pelo administrador.

## <a name="identity-data"></a>Dados de identidade
**P: Porque é que o atributo userPrincipalName (UPN) em Azure AD não corresponde à UPN no local?**  
Para obter informações, consulte estes artigos:

* [Os nomes de utilizador no Microsoft 365, Azure ou Intune não correspondem ao UPN no local ou ao ID de login alternativo](https://mskb.pkisolutions.com/kb/2523192)
* [As alterações não são sincronizadas pela ferramenta de sincronização do Azure Ative Directory depois de alterar a UPN de uma conta de utilizador para utilizar um domínio federado diferente](https://mskb.pkisolutions.com/kb/2669550)

Também pode configurar a AD Azure para permitir que o motor de sincronização atualize a UPN, conforme descrito nas [funções de serviço de sincronização Azure AD Connect](how-to-connect-syncservice-features.md).

**P: É suportado para combinar suavemente um grupo AZure AD ou objeto de contacto com um grupo AD Azure existente ou objeto de contacto?**  
Sim, esta combinação suave é baseada no proxyAddress. A correspondência suave não é suportada para grupos que não estejam habilitados por correio.

**P: É suportado para definir manualmente o atributo ImuttableId num grupo AD Azure existente ou objeto de contacto para o combinar com um grupo AD azure no local ou objeto de contacto?**  
Não, definir manualmente o atributo ImmutableId num grupo AD Azure existente ou objeto de contacto para difícil correspondência não é atualmente suportado.

## <a name="custom-configuration"></a>Configuração personalizada
**P: Onde estão documentados os cmdlets PowerShell para Azure AD Connect?**  
Com exceção dos cmdlets que estão documentados neste site, outros cmdlets PowerShell encontrados no Azure AD Connect não são suportados para uso do cliente.

**P: Posso utilizar a opção "Exportação/importação de servidores" encontrada no Gestor de Serviços de Sincronização para mover a configuração entre servidores?**  
N.º Esta opção não recupera todas as definições de configuração e não deve ser utilizada. Em vez disso, utilize o assistente para criar a configuração base no segundo servidor e use o editor de regras de sincronização para gerar scripts PowerShell para mover qualquer regra personalizada entre servidores. Para mais informações, consulte [a migração swing.](how-to-upgrade-previous-version.md#swing-migration)

**P: As palavras-passe podem ser em cache para a página de início de Sação Azure, e este cache pode ser evitado porque contém um elemento de entrada de palavra-passe com o atributo *autocomplete = atributo "falso"?***  
Atualmente, a modificação dos atributos HTML do campo **Palavra-Passe,** incluindo a etiqueta autocompleta, não é suportada. Estamos neste momento a trabalhar numa funcionalidade que permite o JavaScript personalizado, que permite adicionar qualquer atributo ao campo **Palavra-Passe.**

**P: A página de insusimento do Azure exibe os nomes de utilizadores que já assinaram com sucesso. Este comportamento pode ser desligado?**  
Atualmente, a modificação dos atributos HTML do campo de entrada **password,** incluindo a etiqueta autocompleta, não é suportada. Estamos neste momento a trabalhar numa funcionalidade que permite o JavaScript personalizado, que permite adicionar qualquer atributo ao campo **Palavra-Passe.**

**P: Existe uma maneira de prevenir sessões simultâneas?**  
N.º

## <a name="auto-upgrade"></a>Atualização automática

**P: Quais são as vantagens e consequências da utilização de um upgrade automático?**  
Estamos a aconselhar todos os clientes a permitirem um upgrade automático para a sua instalação Azure AD Connect. O benefício é que recebe sempre os patches mais recentes, incluindo atualizações de segurança para vulnerabilidades que foram encontradas no Azure AD Connect. O processo de upgrade é indolor e acontece automaticamente assim que uma nova versão está disponível. Muitos milhares de clientes Azure AD Connect usam atualização automática a cada nova versão.

O processo de atualização automática estabelece sempre primeiro se uma instalação é elegível para upgrade automático. Se for elegível, a atualização é realizada e testada. O processo inclui também a procura de alterações personalizadas às regras e fatores ambientais específicos. Se os testes mostrarem que uma atualização não foi bem sucedida, a versão anterior é automaticamente restaurada.

Dependendo do tamanho do ambiente, o processo pode demorar algumas horas. Enquanto a atualização está em andamento, não existe sincronização entre o Windows Server Ative Directory e o AD Azure.

**P: Recebi um e-mail a dizer-me que a minha atualização automática já não funciona e preciso de instalar uma nova versão. Porque é que tenho de fazer isto?**  
No ano passado, lançámos uma versão do Azure AD Connect que, em determinadas circunstâncias, poderia ter desativado a funcionalidade de atualização automática no seu servidor. Corrigimos o problema na versão 1.1.750.0 do Azure AD Connect. Se tiver sido afetado pelo problema, pode atenuá-lo executando um script PowerShell para repará-lo ou atualizando manualmente para a versão mais recente do Azure AD Connect. 

Para executar o script PowerShell, [descarregue o script](/samples/browse/?redirectedfrom=TechNet-Gallery) e execute-o no servidor Azure AD Connect numa janela administrativa do PowerShell. Para aprender a executar o script, [veja este pequeno vídeo](https://aka.ms/repairaadcau).

Para fazer o upgrade manual, tem de descarregar e executar a versão mais recente do ficheiro AADConnect.msi.
 
-  Se a sua versão atual tiver mais de 1.1.750.0, [faça o download e faça o upgrade para a versão mais recente](https://www.microsoft.com/download/details.aspx?id=47594).
- Se a sua versão Azure AD Connect for 1.1.750.0 ou mais tarde, não é necessária mais nenhuma ação. Já está a utilizar a versão que contém a correção de atualização automática. 

**P: Recebi um e-mail a dizer-me para atualizar para a versão mais recente para reativar a atualização automática. Estou a usar a versão 1.1.654.0. Preciso de atualizar?**  
Sim, precisa de atualizar para a versão 1.1.750.0 ou mais tarde para voltar a ativar a atualização automática. [Faça o download e o upgrade para a versão mais recente.](https://www.microsoft.com/download/details.aspx?id=47594)

**P: Recebi um e-mail a dizer-me para atualizar para a versão mais recente para reativar a atualização automática. Se eu tiver usado o PowerShell para ativar o upgrade automático, ainda preciso de instalar a versão mais recente?**  
Sim, ainda precisa de atualizar para a versão 1.1.750.0 ou mais tarde. Ativar o serviço de atualização automática com o PowerShell não atenua o problema de atualização automática encontrado nas versões antes do 1.1.750.0.

**P: Quero atualizar para uma versão mais recente, mas não tenho a certeza de quem instalou o Azure AD Connect, e não temos o nome de utilizador e a palavra-passe. Precisamos disto?**
Não precisa de saber o nome de utilizador e a palavra-passe que foi inicialmente utilizado para atualizar o Azure AD Connect. Utilize qualquer conta AD Azure que tenha o papel de Administrador Global.

**P: Como posso encontrar qual versão do Azure AD Connect estou a usar?**  
Para verificar qual a versão do Azure AD Connect instalada no seu servidor, vá ao Painel de Controlo e procure a versão instalada do Microsoft Azure AD Connect selecionando  >  **Programas e Funcionalidades**, como mostrado aqui:

![Versão Azure AD Connect no Painel de Controlo](./media/reference-connect-faq/faq1.png)

**P: Como faço o upgrade para a versão mais recente do Azure AD Connect?**  
Para aprender a fazer upgrade para a versão mais recente, consulte [Azure AD Connect: Upgrade de uma versão anterior para a mais recente](how-to-upgrade-previous-version.md). 

**P: Já atualizámos para a versão mais recente do Azure AD Connect no ano passado. Precisamos de atualizar de novo?**  
A equipa Azure AD Connect faz atualizações frequentes ao serviço. Para beneficiar de correções de bugs e atualizações de segurança, bem como de novas funcionalidades, é importante manter o seu servidor atualizado com a versão mais recente. Se ativar a atualização automática, a sua versão de software é atualizada automaticamente. Para encontrar o histórico de lançamento da versão do Azure AD Connect, consulte [Azure AD Connect: Histórico de lançamento da versão](reference-connect-version-history.md).

**P: Quanto tempo demora a realizar a atualização, e qual é o impacto nos meus utilizadores?**  
O tempo necessário para atualizar depende do tamanho do seu inquilino. Para organizações maiores, talvez seja melhor realizar o upgrade à noite ou ao fim de semana. Durante a atualização, não se realiza qualquer atividade de sincronização.

**P: Acredito que atualizei para Azure AD Connect, mas o portal do Office ainda menciona DirSync. Por que é isto?**  
A equipa do Office está a trabalhar para obter as atualizações do portal do Office para refletir o nome atual do produto. Não reflete a ferramenta de sincronização que está a utilizar.

**Q: O meu estado de atualização automática diz: "Suspenso". Por que está suspenso? Devo ative-lo?**  
Um bug foi introduzido numa versão anterior que, em certas circunstâncias, deixa o estado de atualização automática definido para "Suspenso". Permitir manualmente é tecnicamente possível, mas exigiria vários passos complexos. O melhor que pode fazer é instalar a versão mais recente do Azure AD Connect.

**P: A minha empresa tem requisitos rigorosos de gestão de mudanças, e quero controlar quando for empurrado para fora. Posso controlar quando a atualização automática é lançada?**  
Não, não existe tal característica hoje. A funcionalidade está a ser avaliada para um lançamento futuro.

**P: Receberei um e-mail se a atualização automática falhar? Como saberei que foi um sucesso?**  
Não será notificado do resultado da atualização. A funcionalidade está a ser avaliada para um lançamento futuro.

**P: Publica uma linha do tempo para quando planeia fazer upgrades de automóveis?**  
A atualização automática é o primeiro passo no processo de lançamento de uma versão mais recente. Sempre que há uma nova versão, as atualizações são empurradas automaticamente. As versões mais recentes do Azure AD Connect são pré-anunciadas no [Roteiro AD AZure](../fundamentals/whats-new.md).

**P: A atualização automática também atualiza Azure AD Connect Health?**  
Sim, a atualização automática também atualiza Azure AD Connect Health.

**P: Também atualiza automaticamente os servidores Azure AD Connect no modo de preparação?**  
Sim, pode atualizar automaticamente um servidor Azure AD Connect que está em modo de preparação.

**P: Se a atualização automática falhar e o meu servidor Azure AD Connect não arrancar, o que devo fazer?**  
Em casos raros, o serviço Azure AD Connect não começa depois de realizar a atualização. Nestes casos, reiniciar o servidor normalmente corrige o problema. Se o serviço Azure AD Connect ainda não arrancar, abra um bilhete de apoio. Para obter mais informações, consulte [Criar um pedido de serviço para contactar o suporte do Microsoft 365](/archive/blogs/praveenkumar/how-to-create-service-requests-to-contact-office-365-support). 

**P: Não sei quais são os riscos quando faço upgrade para uma versão mais recente do Azure AD Connect. Pode ligar-me para me ajudar com a atualização?**  
Se precisar de ajuda para atualizar para uma versão mais recente do Azure AD Connect, abra um bilhete de suporte na Create um pedido de [serviço para contactar o suporte da Microsoft 365](/archive/blogs/praveenkumar/how-to-create-service-requests-to-contact-office-365-support).

## <a name="operational-best-practice"></a>Melhores práticas operacionais    
Abaixo estão algumas das melhores práticas que deve implementar ao sincronizar entre o Windows Server Ative Directory e o Azure Ative Directory.

**Aplicar autenticação multi-factor para todas as contas sincronizadas** A autenticação multi-factor Azure AD ajuda a salvaguardar o acesso a dados e aplicações, mantendo a simplicidade para os utilizadores. Proporciona segurança adicional exigindo uma segunda forma de autenticação e proporciona uma autenticação forte através de uma gama de métodos de autenticação fáceis de usar. Os utilizadores podem ou não ser desafiados para mFA com base nas decisões de configuração que um administrador toma. Pode ler mais sobre mFA aqui: https://www.microsoft.com/security/business/identity/mfa?rtc=1

**Siga as diretrizes de segurança do servidor Azure AD Connect** O servidor Azure AD Connect contém dados de identidade críticos e deve ser tratado como um componente tier 0, conforme documentado no [modelo de nível administrativo](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)do Diretório Ativo . Consulte também [as nossas diretrizes para assegurar o seu servidor AADConnect](./how-to-connect-install-prerequisites.md#azure-ad-connect-server).

**Ativar o PHS para deteção de credenciais vazadas** O Password Hash Sync também permite [a deteção de credenciais vazadas](../identity-protection/concept-identity-protection-risks.md) para as suas contas híbridas. A Microsoft trabalha ao lado de pesquisadores web escuros e agências de aplicação da lei para encontrar pares de nome de utilizador/palavra-passe disponíveis ao público. Se algum destes pares corresponder aos dos seus utilizadores, a conta associada é transferida para um risco elevado. 


## <a name="troubleshooting"></a>Resolução de problemas
**P: Como posso obter ajuda com a Azure AD Connect?**

[Pesse na Base de Conhecimento da Microsoft (KB)](https://www.microsoft.com/en-us/search/result.aspx?q=azure+active+directory+connect)

* Procure soluções técnicas para obter soluções comuns sobre o suporte ao Azure AD Connect.

[Microsoft Q&Uma página de perguntas para o Azure Ative Directory](/answers/topics/azure-active-directory.html)

* Procure perguntas técnicas e respostas ou faça as suas próprias perguntas indo para [a comunidade AZure AD](/answers/topics/azure-active-directory.html).

[Obter suporte para o Azure AD](../fundamentals/active-directory-troubleshooting-support-howto.md)

**P: Porque estou a ver os Eventos 6311 e 6401 ocorrerem após erros de sync step?**

Os eventos 6311 - **O servidor encontrou um erro inesperado durante a realização de uma chamada** e 6401 - O controlador do agente de **gestão encontrou um erro inesperado** - são sempre registados após um erro de sincronização. Para resolver estes erros, é necessário limpar os erros do passo de sincronização.  Para obter mais informações, consulte [erros de resolução de problemas durante a sincronização](tshoot-connect-sync-errors.md) e [sincronização de objetos de resolução de problemas com sincronização de Azure AD Connect](tshoot-connect-objectsync.md)