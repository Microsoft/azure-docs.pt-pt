---
title: Diretório Ativo Azure Connect FAQ - Microsoft Docs
description: Este artigo responde frequentemente a perguntas sobre azure AD Connect.
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
ms.openlocfilehash: e5aa30bc819531ee8cc9cd337648a6cbc661bb29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77149818"
---
# <a name="azure-active-directory-connect-faq"></a>Azure Ative Directory Connect FAQ

## <a name="general-installation"></a>Instalação geral

**P: Como posso endurecer o meu servidor Azure AD Connect para diminuir a superfície de ataque de segurança?**

A Microsoft recomenda o endurecimento do seu servidor Azure AD Connect para diminuir a superfície de ataque de segurança para este componente crítico do seu ambiente informático.  Seguindo as recomendações abaixo diminuirá os riscos de segurança para a sua organização.

* Implemente o Azure AD Connect num servidor de domínio e restrinja o acesso administrativo a administradores de domínio ou a outros grupos de segurança fortemente controlados

Para saber mais, consulte: 

* [Assegurar grupos de administradores](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Assegurar contas de administrador incorporados](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Melhoria e sustentação da segurança através da redução das superfícies de ataque](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Redução da superfície de ataque do Diretório Ativo](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

**P: A instalação funcionará se o Azure Ative Directory (Azure AD) Global Admin tiver a autenticação de dois fatores (2FA) ativada?**  
A partir das construções de fevereiro de 2016, este cenário é apoiado.

**P: Existe uma maneira de instalar o Azure AD Connect sem vigilância?**  
A instalação Azure AD Connect só é suportada quando utilizar o assistente de instalação. Uma instalação silenciosa e sem vigilância não é suportada.

**P: Tenho uma floresta onde um domínio não pode ser contactado. Como posso instalar o Azure AD Connect?**  
A partir das construções de fevereiro de 2016, este cenário é apoiado.

**P: O agente de saúde Azure Ative Directory Domain Services (Azure AD DS) funciona no núcleo do servidor?**  
Sim. Depois de instalar o agente, pode concluir o processo de registo utilizando o seguinte cmdlet PowerShell: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**P: O Suporte Azure AD Connect sincronização de dois domínios para um AD Azure?**  
Sim, este cenário é apoiado. Consulte [vários domínios.](how-to-connect-install-multiple-domains.md)
 
**P: Pode ter vários conectores para o mesmo domínio de Diretório Ativo no Azure AD Connect?**  
Não, não são suportados múltiplos conectores para o mesmo domínio AD. 

**P: Posso mover a base de dados Azure AD Connect da base de dados local para uma instância remota do Servidor SQL?**   
Sim, os seguintes passos fornecem orientações gerais sobre como fazê-lo. Estamos neste momento a trabalhar num documento mais detalhado.
1. Volte a ser a base de dados LocalDB ADSync.
A forma mais simples de o fazer é utilizar o Estúdio de Gestão de Servidores SQL instalado na mesma máquina que o Azure AD Connect. Ligue-se a *(LocalDb).\ADSync*, e, em seguida, volte a ser a base de dados ADSync.

2. Devolva a base de dados ADSync à sua instância remota do Servidor SQL.

3. Instale o Azure AD Connect contra a base de [dados SQL remota](how-to-connect-install-existing-database.md)existente .
   O artigo demonstra como migrar para a utilização de uma base de dados Local SQL. Se estiver a migrar para a utilização de uma base de dados SQL remota, no passo 5 do processo, também deve introduzir uma conta de serviço existente que o serviço Windows Sync funcionará como. Esta conta de serviço sincronizado do motor é descrita aqui:
   
      **Utilize uma conta**de serviço existente : Por padrão, o Azure AD Connect utiliza uma conta de serviço virtual para os serviços de sincronização utilizarem. Se utilizar uma instância remota do SQL Server ou utilizar um proxy que necessite de autenticação, utilize uma conta de serviço gerida ou uma conta de serviço no domínio e conheça a palavra-passe. Nesses casos, introduza a conta a utilizar. Certifique-se de que os utilizadores que estão a executar a instalação são administradores do sistema no SQL para que possam ser criadas credenciais de login para a conta de serviço. Para mais informações, consulte [as contas e permissões do Azure AD Connect](reference-connect-accounts-permissions.md#adsync-service-account). 
   
      Com a compilação mais recente, o aprovisionamento da base de dados pode agora ser realizado fora de banda pelo administrador SQL e, em seguida, instalado pelo administrador do Azure AD Connect com direitos de proprietário da base de dados. Para mais informações, consulte [Instalar o Azure AD Connect utilizando permissões de administrador delegados DaQL](how-to-connect-install-sql-delegation.md).

Para manter as coisas simples, recomendamos que os utilizadores que instalam o Azure AD Connect sejam administradores do sistema no SQL. No entanto, com construções recentes pode agora utilizar administradores SQL delegados, conforme descrito no [Install Azure AD Connect utilizando permissões de administrador delegados DaQL](how-to-connect-install-sql-delegation.md).

**P: Quais são algumas das melhores práticas do campo?**  

Segue-se um documento informativo que apresenta algumas das melhores práticas que a engenharia, o apoio e os nossos consultores desenvolveram ao longo dos anos.  Isto é apresentado numa lista de balas que pode ser rapidamente referenciada.  Embora esta lista tente ser abrangente, pode haver práticas adicionais que talvez ainda não tenham chegado à lista.

- Se utilizar o Full SQL, então deve permanecer local vs. remoto
    - Menos lúpulo
    - Mais fácil de resolver problemas
    - Menos complexidade
    - Necessidade de designar recursos para a SQL e permitir despesas gerais para Azure AD Connect e OS
- Bypass Proxy se possível, se não conseguir contornar o proxy, então precisa garantir que o valor do tempo de tempo é superior a 5 minutos.
- Se for necessário procuração, deve adicionar o proxy ao ficheiro machine.config
- Esteja atento aos empregos e manutenção locais da SQL e como irão afetar o Azure AD Connect - particularmente a reindexação
- Certifique-se de que o DNS pode resolver externamente
- Certifique-se de que [as especificações](how-to-connect-install-prerequisites.md#hardware-requirements-for-azure-ad-connect) do servidor são por recomendação, quer esteja a utilizar servidores físicos ou virtuais
- Certifique-se de que se estiver a utilizar um servidor virtual que os recursos necessários são dedicados
- Certifique-se de que tem a configuração do disco e do disco para as melhores práticas para o Servidor SQL
- Instalar e configurar a Azure AD Connect Health para monitorização
- Utilize o limiar de eliminação incorporado no Azure AD Connect.
- Reveja cuidadosamente as atualizações de lançamento para estar preparada para todas as alterações e novos atributos que possam ser adicionados
- Backup tudo
    - Chaves de reserva
    - Regras de Sincronização de Backup
    - Configuração do servidor de backup
    - Base de Dados SQL de backup
- Certifique-se de que não existem agentes de backup do terceiro partido que estão a apoiar o SQL sem o SQL VSS Writer (comum em servidores virtuais com fotos de terceiros)
- Limite a quantidade de regras de sincronização personalizadas que são usadas à medida que adicionam complexidade
- Trate os servidores de ligação da AD Azure como servidores de nível 0
- Seja o leery de modificar regras de sincronização em nuvem sem grande compreensão do impacto e dos motoristas de negócios certos
- Certifique-se de que as portas de URL e Firewall corretas estão abertas para suporte à Azure AD Connect e Azure AD Connect Health
- Aproveite o atributo filtrado pela nuvem para resolução de problemas e evite objetos fantasma
- Com o Servidor de Encenação certifique-se de que está a utilizar o Documento de Configuração de Ligação AD Azure para a consistência entre os servidores
- Os Servidores de Encenação devem estar em centros de dados separados (Localizações Físicas
- Os servidores de encenação não são para ser uma solução de Alta Disponibilidade, mas pode ter vários servidores de encenação
- A introdução de um "Lag" Staging Servers pode atenuar algum tempo de paragem em caso de erro
- Teste e valide todas as atualizações no Servidor de Encenação primeiro
- Valide sempre as exportações antes de passar para o servidor de encenação.  Aproveite o servidor de encenação para importações completas e sincronizações completas para reduzir o impacto do negócio
- Mantenha a consistência da versão entre os Servidores de Ligação AD Azur o máximo possível 

**P: Posso permitir que o Azure AD Connect crie a conta de Conector Azure AD na máquina workgroup?**
Não.  Para permitir que o Azure AD Connect crie automaticamente a conta de Conector Azure AD, a máquina deve ser unida pelo domínio.  

## <a name="network"></a>Rede
**P: Tenho uma firewall, um dispositivo de rede ou outra coisa que limita o tempo que as ligações podem permanecer abertas na minha rede. Qual deve ser o limite de tempo limite do meu cliente quando uso o Azure AD Connect?**  
Todo o software de rede, dispositivos físicos ou qualquer outra coisa que limite o tempo máximo que as ligações podem permanecer abertas deve utilizar um limiar de pelo menos cinco minutos (300 segundos) para a conectividade entre o servidor onde o cliente Azure AD Connect está instalado e Diretório Ativo Azure. Esta recomendação aplica-se também a todas as ferramentas de sincronização da Microsoft Identity previamente lançadas.

**P: Os domínios de etiqueta única (SLDs) são suportados?**  
Embora recomendemos vivamente contra esta configuração de rede[(ver artigo),](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains)utilizando o sincronizado Azure AD Connect com um único domínio de etiqueta, desde que a configuração da rede para o domínio de nível único esteja a funcionar corretamente.

**P: As florestas com domínios disconjuntos de AD são suportadas?**  
Não, o Azure AD Connect não suporta florestas no local que contenham espaços de nome sinuosos.

**P: Os nomes NetBIOS "pontilhados" são suportados?**  
Não, o Azure AD Connect não suporta florestas ou domínios no local onde o nome NetBIOS contenha um ponto (.).

**P: O ambiente puro IPv6 é suportado?**  
Não, o Azure AD Connect não suporta um ambiente IPv6 puro.

**Q:I ter um ambiente multi-florestal e a rede entre as duas florestas está a utilizar o NAT (Tradução de Endereços de Rede). A utilização do Azure AD Connect entre estas duas florestas é suportada?**</br>
Não, a utilização do Azure AD Connect sobre o NAT não é suportada. 

## <a name="federation"></a>de Federação
**P: O que faço se receber um e-mail que me pede para renovar o meu certificado office 365?**  
Para obter orientações sobre a renovação do certificado, consulte a renovação dos [certificados.](how-to-connect-fed-o365-certs.md)

**P: Tenho "Automaticamente atualizar a parte de base" para o Office 365 relying party. Tenho de tomar alguma ação quando o meu certificado de assinatura de fichas passar automaticamente?**  
Use a orientação descrita no artigo [renovar certificados.](how-to-connect-fed-o365-certs.md)

## <a name="environment"></a>Ambiente
**P: É suportado para mudar o nome do servidor depois de o Azure AD Connect ter sido instalado?**  
Não. Alterar o nome do servidor torna o motor sincronizado incapaz de se ligar à instância de base de dados SQL, e o serviço não pode arrancar.

**P: As regras de sincronização criptográfica (NGC) da próxima geração são suportadas numa máquina ativada por FIPS?**  
Não.  Não são suportadas.

**P. Se desativei um dispositivo sincronizado (por exemplo: HAADJ) no portal Azure, por que é reativado?**<br>
Os dispositivos sincronizados podem ser da autoria ou dominados nas instalações. Se um dispositivo sincronizado estiver ativado nas instalações, poderá ser reativado no portal Azure, mesmo que tenha sido previamente desativado por um administrador. Para desativar um dispositivo sincronizado, utilize o Diretório Ativo no local para desativar a conta do computador.

**P. Se bloquear o registo de utilizadores no portal Office 365 ou Azure AD para utilizadores sincronizados, por que razão é desbloqueado ao iniciar sessão?**<br>
Os utilizadores sincronizados podem ser da autoria ou dominados nas instalações. Se a conta estiver ativada no local, pode desbloquear o bloco de inscrição colocado pelo administrador.

## <a name="identity-data"></a>Dados de identidade
**P: Por que o atributo do utilizador PrincipalName (UPN) em Azure AD corresponde ao UPN no local?**  
Para obter informações, consulte estes artigos:

* [Nomes de utilizadores no Office 365, Azure ou Intune não correspondem ao UPN ou id de login alternativo](https://support.microsoft.com/kb/2523192)
* [As alterações não são sincronizadas pela ferramenta de sincronização do Diretório Ativo Azure depois de alterar a UPN de uma conta de utilizador para utilizar um domínio federado diferente](https://support.microsoft.com/kb/2669550)

Também pode configurar o Azure AD para permitir que o motor de sincronização atualize o UPN, conforme descrito nas funcionalidades de [sincronização Do Azure AD Connect](how-to-connect-syncservice-features.md).

**P: É suportado para combinar suavemente um grupo de Anúncios Azure ou objeto de contacto com um grupo ou objeto de contacto Azure existente?**  
Sim, este jogo suave é baseado no proxyAddress. A correspondência suave não é suportada para grupos que não estão ativados por correio.

**P: É suportado para definir manualmente o atributo Imutável num grupo AD Azur e objeto de contacto existente para o comparar duramente com um grupo ou objeto de contacto azure no local?**  
Não, configurar manualmente o atributo ImutableId num grupo de AD Azure existente ou objeto de contacto para o hard-match não é suportado atualmente.

## <a name="custom-configuration"></a>Configuração personalizada
**P: Onde estão documentados os cmdlets PowerShell para Azure AD Connect?**  
Com exceção dos cmdlets que estão documentados neste site, outros cmdlets PowerShell encontrados no Azure AD Connect não são suportados para uso do cliente.

**P: Posso usar a opção "Server export/server import" que é encontrada no Gestor de Serviços de Sincronização para mover a configuração entre servidores?**  
Não. Esta opção não recupera todas as definições de configuração e não deve ser utilizada. Em vez disso, utilize o assistente para criar a configuração base no segundo servidor e utilize o editor de regras de sincronização para gerar scripts PowerShell para mover qualquer regra personalizada entre servidores. Para mais informações, consulte a [migração do Swing.](how-to-upgrade-previous-version.md#swing-migration)

**P: As palavras-passe podem ser colocadas em cache para a página de entrada do Azure, e este cache pode ser evitado porque contém um elemento de entrada de senha com o atributo *autocompleto = "falso"?***  
Atualmente, a modificação dos atributos HTML do campo **Password,** incluindo a etiqueta autocompleta, não é suportada. Estamos neste momento a trabalhar numa funcionalidade que permite o JavaScript personalizado, que permite adicionar qualquer atributo ao campo **Password.**

**P: A página de entrada do Azure exibe os nomes de utilizador dos utilizadores que já assinaram com sucesso. Este comportamento pode ser desligado?**  
Atualmente, não é suportado modificar os atributos HTML do campo de entrada **password,** incluindo a etiqueta autocompleta. Estamos neste momento a trabalhar numa funcionalidade que permite o JavaScript personalizado, que permite adicionar qualquer atributo ao campo **Password.**

**P: Existe uma maneira de prevenir sessões simultâneas?**  
Não.

## <a name="auto-upgrade"></a>Atualização automática

**P: Quais são as vantagens e consequências da utilização da atualização automática?**  
Aconselhamos todos os clientes a permitir em atualização automática para a sua instalação Azure AD Connect. O benefício é que recebe sempre os patches mais recentes, incluindo atualizações de segurança para vulnerabilidades que foram encontradas no Azure AD Connect. O processo de atualização é indolor e acontece automaticamente assim que uma nova versão está disponível. Muitos milhares de clientes da Azure AD Connect utilizam o upgrade automático a cada novo lançamento.

O processo de atualização automática estabelece sempre se uma instalação é elegível para atualização automática. Se for elegível, a atualização é realizada e testada. O processo inclui também a procura de alterações personalizadas às regras e fatores ambientais específicos. Se os testes mostrarem que uma atualização não foi bem sucedida, a versão anterior é restaurada automaticamente.

Dependendo do tamanho do ambiente, o processo pode demorar algumas horas. Enquanto a atualização está em andamento, não acontece nenhuma sincronização entre o Diretório Ativo do Windows Server e o Azure AD.

**P: Recebi um e-mail a dizer-me que a minha atualização automática já não funciona e preciso de instalar uma nova versão. Por que preciso fazer isto?**  
No ano passado, lançámos uma versão do Azure AD Connect que, sob certas circunstâncias, poderia ter desativado a funcionalidade de atualização automática no seu servidor. Corrigimos o problema na versão 1.1.750.0 do Azure AD Connect. Se foi afetado pelo problema, pode atenuá-lo executando um script PowerShell para repará-lo ou atualizando manualmente para a versão mais recente do Azure AD Connect. 

Para executar o script PowerShell, [descarregue o script](https://aka.ms/repairaadconnect) e execute-o no seu servidor Azure AD Connect numa janela administrativa da PowerShell. Para aprender a executar o guião, [veja este pequeno vídeo.](https://aka.ms/repairaadcau)

Para atualizar manualmente, tem de descarregar e executar a versão mais recente do ficheiro AADConnect.msi.
 
-  Se a sua versão atual for superior a 1.1.750.0, [descarregue e atualize para a versão mais recente.](https://www.microsoft.com/download/details.aspx?id=47594)
- Se a sua versão Azure AD Connect for 1.1.750.0 ou posterior, não é necessária qualquer outra ação. Já está a usar a versão que contém a correção de atualização automática. 

**P: Recebi um e-mail a dizer-me para fazer upgrade para a versão mais recente para reativar a atualização automática. Estou a usar a versão 1.1.654.0. Preciso fazer upgrade?**  
Sim, precisa de fazer upgrade para a versão 1.1.750.0 ou mais tarde para reativar a atualização automática. [Descarregue e atualize para a versão mais recente.](https://www.microsoft.com/download/details.aspx?id=47594)

**P: Recebi um e-mail a dizer-me para fazer upgrade para a versão mais recente para reativar a atualização automática. Se usei o PowerShell para permitir a atualização automática, ainda preciso de instalar a versão mais recente?**  
Sim, ainda precisa de fazer upgrade para a versão 1.1.750.0 ou mais tarde. Ativar o serviço de atualização automática com a PowerShell não atenua o problema de atualização automática encontrado nas versões antes de 1.1.750.0.

**P: Quero fazer upgrade para uma versão mais recente, mas não sei quem instalou o Azure AD Connect, e não temos o nome de utilizador e senha. Precisamos disto?**
Não é necessário saber o nome de utilizador e a palavra-passe que foi inicialmente utilizado para atualizar o Azure AD Connect. Utilize qualquer conta Azure AD que tenha o papel de Administrador Global.

**P: Como posso encontrar qual a versão do Azure AD Connect que estou a usar?**  
Para verificar qual a versão do Azure AD Connect instalada no seu servidor, vá ao Painel de Controlo e procure a versão instalada do Microsoft Azure AD Connect selecionando Programas e **Funcionalidades** > **Programs and Features**de Programas, como mostrado aqui:

![Versão Azure AD Connect no Painel de Controlo](./media/reference-connect-faq/faq1.png)

**P: Como faço upgrade para a versão mais recente do Azure AD Connect?**  
Para saber como fazer upgrade para a versão mais recente, consulte [o Azure AD Connect: Upgrade de uma versão anterior para a mais recente](how-to-upgrade-previous-version.md). 

**P: Já atualizámos para a versão mais recente do Azure AD Connect no ano passado. Precisamos fazer upgrade de novo?**  
A equipa Azure AD Connect faz atualizações frequentes ao serviço. Para beneficiar de correções de bugs e atualizações de segurança, bem como de novas funcionalidades, é importante manter o servidor atualizado com a versão mais recente. Se ativar a atualização automática, a versão do software é atualizada automaticamente. To find the version release history of Azure AD Connect, see [Azure AD Connect: Version release history](reference-connect-version-history.md).

**P: Quanto tempo demora a realizar a atualização e qual é o impacto nos meus utilizadores?**  
O tempo necessário para melhorar depende do tamanho do seu inquilino. Para organizações maiores, talvez seja melhor realizar a atualização à noite ou ao fim de semana. Durante a atualização, não ocorre nenhuma atividade de sincronização.

**P: Acredito que atualizei para o Azure AD Connect, mas o portal do Office ainda menciona o DirSync. Por que é isto?**  
A equipa do Office está a trabalhar para obter as atualizações do portal do Office para refletir o nome atual do produto. Não reflete qual a ferramenta de sincronização que está a usar.

**P: O meu estado de atualização automática diz: "Suspenso". Por que está suspenso? Devo permitir?**  
Um bug foi introduzido numa versão anterior que, em certas circunstâncias, deixa o estado de atualização automática definido para "Suspenso". Permitir manualmente é tecnicamente possível, mas exigiria várias etapas complexas. O melhor que pode fazer é instalar a versão mais recente do Azure AD Connect.

**P: A minha empresa tem rigorosos requisitos de gestão de mudanças, e quero controlar quando for adiada. Posso controlar quando a atualização automática for lançada?**  
Não, não existe tal característica hoje. A funcionalidade está a ser avaliada para uma futura versão.

**P: Receberei um e-mail se a atualização automática falhar? Como vou saber que foi um sucesso?**  
Não será notificado do resultado da atualização. A funcionalidade está a ser avaliada para uma futura versão.

**P: Publica uma cronologia para quando planeia adiar atualizações automáticas?**  
A atualização automática é o primeiro passo no processo de lançamento de uma versão mais recente. Sempre que há um novo lançamento, as atualizações são empurradas automaticamente. As versões mais recentes do Azure AD Connect são pré-anunciadas no [Azure AD Roadmap](../fundamentals/whats-new.md).

**P: A atualização automática também atualiza a Azure AD Connect Health?**  
Sim, a atualização automática também atualiza a Azure AD Connect Health.

**P: Também atualiza automaticamente os servidores Azure AD Connect no modo de encenação?**  
Sim, pode atualizar automaticamente um servidor Azure AD Connect que se encontra em modo de encenação.

**P: Se a atualização automática falhar e o meu servidor Azure AD Connect não começar, o que devo fazer?**  
Em casos raros, o serviço Azure AD Connect não arranca depois de realizar a atualização. Nestes casos, reiniciar o servidor normalmente corrige o problema. Se o serviço Azure AD Connect ainda não arrancar, abra um bilhete de apoio. Para mais informações, consulte Criar um pedido de serviço para contactar o [Suporte do Office 365](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/). 

**P: Não sei quais são os riscos quando atualizo para uma versão mais recente do Azure AD Connect. Pode ligar-me para me ajudar na atualização?**  
Se precisar de ajuda para atualizar para uma versão mais recente do Azure AD Connect, abra um bilhete de apoio no Create um pedido de serviço para contactar o suporte do [Office 365](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/).

## <a name="troubleshooting"></a>Resolução de problemas
**P: Como posso obter ajuda com o Azure AD Connect?**

[Pesquise a Base de Conhecimento da Microsoft (KB)](https://www.microsoft.com/en-us/search/result.aspx?q=azure+active+directory+connect)

* Procure no KB soluções técnicas para problemas comuns de correção de rutura seletivas sobre suporte para Azure AD Connect.

[Fóruns de Diretório Ativo Azure](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Procure perguntas e respostas técnicas ou faça as suas próprias perguntas indo para [a comunidade ad.](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)

[Obter suporte para o Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

**P: Porque é que estou a ver os Eventos 6311 e 6401 a ocorrerem após erros de sync step?**

Os eventos 6311 - **O servidor encontrou um erro inesperado ao realizar uma chamada** e 6401 - O controlador de agente de **gestão encontrou um erro inesperado** - são sempre registados após um erro de etapa de sincronização. Para resolver estes erros, é necessário limpar os erros de etapa de sincronização.  Para mais informações, consulte erros de [resolução de problemas durante](tshoot-connect-sync-errors.md) a sincronização e sincronização de [objetos de Troubleshoot com sincronização azure AD Connect](tshoot-connect-objectsync.md)
