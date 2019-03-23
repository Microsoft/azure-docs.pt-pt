---
title: Integração do Active Directory – o Azure Active Directory e do servidor MFA do Azure
description: Como integrar o Servidor Multi-Factor Authentication do Azure com o Active Directory para que possa sincronizar os diretórios.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: f97b4ee364ecadde7738b8fe077f21d5732365f6
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371830"
---
# <a name="directory-integration-between-azure-mfa-server-and-active-directory"></a>Integração de diretórios entre o Servidor MFA do Azure e o Active Directory

Utilize a secção Integração de Diretório do Servidor MFA do Azure para integrar no Active Directory ou noutro diretório LDAP. Pode configurar atributos para corresponder ao esquema de diretório e configurar a sincronização de utilizadores automática.

## <a name="settings"></a>Definições

Por predefinição, o Servidor Multi-Factor Authentication (MFA) do Azure está configurado para importar ou sincronizar os utilizadores do Active Directory.  O separador Integração de Diretório permite-lhe substituir o comportamento predefinido e vincular a um diretório LDAP diferente, a um diretório de ADAM ou a um controlador de domínio do Active Directory específico.  Também prevê a utilização de Autenticação LDAP para o LDAP do proxy ou para o Enlace de LDAP como um destino RADIUS, pré-autenticação para Autenticação do IIS ou autenticação primária para o Portal de Utilizador.  A tabela seguinte descreve as definições individuais.

![Editar configuração de LDAP no servidor MFA](./media/howto-mfaserver-dir-ad/dirint.png)

| Funcionalidade | Descrição |
| --- | --- |
| Utilizar o Active Directory |Selecione a opção Utilizar o Active Directory para utilizar o Active Directory para importação e sincronização.  Esta é a predefinição. <br>Nota: Para integração do Active Directory funcione corretamente, associe o computador a um domínio e iniciar sessão com uma conta de domínio. |
| Incluir domínios fidedignos |Marque **Incluir Domínios Fidedignos** para que o agente tente ligar a domínios que o domínio atual considere fidedignos, a outro domínio na floresta ou a domínios envolvidos numa confiança de floresta.  Quando não estiver a importar ou a sincronizar utilizadores de qualquer um dos domínios fidedignos, desmarque a caixa de verificação para melhorar o desempenho.  A predefinição é a caixa marcada. |
| Utilizar configuração de LDAP específica |Selecione a opção Utilizar LDAP para utilizar as definições de LDAP especificadas para a importação e a sincronização. Nota: Quando utilizar LDAP está selecionada, a interface de utilizador altera as referências do Active Directory para LDAP. |
| Botão Editar |O botão Editar permite que as definições de configuração de LDAP atuais sejam modificadas. |
| Utilizar consultas de âmbito de atributos |Indica se devem ser utilizadas consultas de âmbito de atributos.  As consultas de âmbito de atributos permitem registos qualificativos de procuras em diretórios eficientes com base nas entradas no atributo de outro registo.  O Servidor Multi-Factor Authentication do Azure utiliza consultas de âmbito de atributos para consultar de forma eficiente os utilizadores que são membros de um grupo de segurança.   <br>Nota:  Existem alguns casos em que as consultas de âmbito de atributo são suportadas, mas não devem ser usadas.  Por exemplo, o Active Directory pode ter problemas com as consultas de âmbito de atributos quando um grupo de segurança contém membros de mais de um domínio. Neste caso, anule a seleção da caixa de verificação. |

A tabela seguinte descreve as definições de configuração de LDAP.

| Funcionalidade | Descrição |
| --- | --- |
| Servidor |Introduza o nome do anfitrião ou endereço IP do servidor que executa o diretório LDAP.  Também pode ser especificado um servidor de cópia de segurança, separado por ponto e vírgula. <br>Nota: Quando o tipo de enlace é SSL, um nome de anfitrião totalmente qualificado é necessário. |
| DN Base |Introduza o nome único do objeto de diretório base a partir do qual são iniciadas todas as consultas de diretório.  Por exemplo, dc=abc,dc=com. |
| Tipo de enlace - Consultas |Selecione o tipo de enlace adequado para utilizar quando se vincula a procurar no diretório LDAP.  Este serve para importações, sincronização e resolução de nomes de utilizador. <br><br>  Anónimo - é feito um enlace anónimo.  DN do Enlace e Palavra-passe do Enlace não são utilizados.  Só funciona se o diretório LDAP permitir o enlace anónimo e as permissões permitirem a consulta dos registos e atributos adequados.  <br><br> Simples - o DN do Enlace e a Palavra-passe do Enlace são transmitidos como texto simples para vincular ao diretório LDAP.  Só deve ser utilizada para fins de teste, para verificar se é possível aceder ao servidor e se a conta de enlace tem o acesso adequado. Depois de ter sido instalado o certificado adequado, opte por utilizar SSL.  <br><br> SSL - o DN do Enlace e a Palavra-passe do Enlace são encriptados com SSL para se vincularem ao diretório LDAP.  Instale localmente um certificado em que o diretório LDAP confie.  <br><br> Windows - o DN do Enlace e a Palavra-passe do Enlace são utilizados para ligar em segurança a um controlador de domínio do Active Directory ou ao diretório de ADAM.  Se o campo Nome de utilizador do enlace for deixado em branco, a conta do utilizador com sessão iniciada é utilizada para o enlace. |
| Tipo de enlace - Autenticações |Selecione o tipo de enlace adequado para utilizar quando efetuar a autenticação do enlace de LDAP.  Veja as descrições dos tipos de enlace em Tipo de enlace - Consultas.  Por exemplo, esta ação permite a utilização do enlace Anónimo para consultas enquanto o enlace de SSL serve para proteger as autenticações de enlace de LDAP. |
| DN do Enlace ou Nome de utilizador do enlace |Introduza o nome único do registo de utilizador para a conta a utilizar ao fazer o enlace ao diretório LDAP.<br><br>O nome único do enlace só é utilizado quando o Tipo de enlace for Simples ou SSL.  <br><br>Introduza o nome de utilizador da conta Windows a utilizar quando faz o enlace ao diretório LDAP quando o Tipo de enlace é Windows.  Se o campo for deixado em branco, a conta do utilizador com sessão iniciada é utilizada para o enlace. |
| Palavra-passe do Enlace |Introduza a palavra-passe do enlace para o nome de utilizador ou DN do Enlace que está a ser utilizado para fazer o enlace ao diretório LDAP.  Para configurar a palavra-passe para o Serviço AdSync do Servidor Multi-Factor Auth, ative a sincronização e confirme que o serviço está em execução no computador local.  A palavra-passe é guardada nos Nomes de utilizador e palavras-passe armazenadas do Windows, sob a conta como a qual o Serviço AdSync do Servidor Multi-Factor Auth está a ser executado.  A palavra-passe também é guardada sob a conta como a qual a interface de utilizador do Servidor Multi-Factor Auth está a ser executada e sob a conta como a qual o Serviço do Servidor Multi-Factor Auth está a ser executado.  <br><br>Uma vez que a palavra-passe só é armazenada nos Nomes de utilizador e palavras-passe armazenadas do Windows do servidor local, repita este passo em cada Servidor Multi-Factor Auth que precisa de acesso à palavra-passe. |
| Tamanho limite da consulta |Especifique o tamanho limite para o número máximo de utilizadores que uma pesquisa de diretório vai devolver.  Este limite deve corresponder à configuração no diretório LDAP.  Para grandes pesquisas onde a paginação não é suportada, a importação e a sincronização tentam obter utilizadores em lotes.  Se o tamanho limite aqui especificado for maior do que o limite configurado no diretório LDAP, alguns utilizadores podem ser omitidos. |
| Botão Testar |Clique em **Testar** para testar o enlace ao servidor LDAP.  <br><br>Para testar o enlace, não precisa de selecionar a opção **Utilizar LDAP**. Desta forma, é possível testar o enlace antes de utilizar a configuração de LDAP. |

## <a name="filters"></a>Filtros

Os filtros permitem definir critérios para qualificar registos ao realizar uma pesquisa de diretório.  Ao definir o filtro, pode determinar o âmbito dos objetos que quer sincronizar.  

![Configurar a filtragem de diretório no servidor MFA](./media/howto-mfaserver-dir-ad/dirint2.png)

O Multi-Factor Authentication do Azure tem as três opções de filtros seguintes

* **Filtro de contentores** -Especifique os critérios de filtro utilizados para qualificar registos de contentores ao realizar uma pesquisa de diretório.  Para o Active Directory e ADAM, é normalmente utilizado (|(objectClass=organizationalUnit)(objectClass=container)).  Para outros diretórios LDAP, utilize critérios de filtro que qualifiquem cada tipo de objeto de contentor, consoante o esquema do diretório.  <br>Nota:  Se deixado em branco, ((objectClass é utilizado por predefinição.
* **Filtro de grupos de segurança** - Especifique os critérios de filtro utilizados para qualificar registos de grupos de segurança ao realizar uma pesquisa de diretório.  Para o Active Directory e ADAM, é normalmente utilizado, (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)).  Para outros diretórios LDAP, utilize critérios de filtro que qualifiquem cada tipo de grupo de segurança, consoante o esquema do diretório.  <br>Nota:  Se deixado em branco, (&(objectCategory=group) (groupType:1.2.840.113556.1.4.804:=-2147483648)) é utilizado por predefinição.
* **Filtro de utilizador** - Especifique os critérios de filtro utilizados para qualificar registos de utilizadores ao realizar uma pesquisa de diretório.  Para o Active Directory e ADAM, é normalmente utilizado (&(objectClass=user)(objectCategory=person)).  Para outros diretórios LDAP, utilize (objectClass=inetOrgPerson) ou algo semelhante, consoante o esquema do diretório. <br>Nota:  Se deixado em branco, (& (objectCategory é utilizado por predefinição.

## <a name="attributes"></a>Atributos

Pode personalizar os atributos conforme necessário para um diretório específico.  Isto permite-lhe adicionar atributos personalizados e ajustar a sincronização para apenas os atributos de que precisa. Utilize o nome do atributo conforme definido no esquema do diretório para o valor de cada campo de atributo. A tabela seguinte disponibiliza informações adicionais sobre cada funcionalidade.

Os atributos podem ser introduzidos manualmente e não precisam de corresponder a um atributo da lista de atributos.

![Personalizar os atributos de integração de diretório no servidor MFA](./media/howto-mfaserver-dir-ad/dirint3.png)

| Funcionalidade | Descrição |
| --- | --- |
| Identificador exclusivo |Introduza o nome de atributo do atributo que funciona como o identificador exclusivo do contentor, do grupo de segurança e de registos de utilizador.  No Active Directory, normalmente é objectGUID. Outras implementações de LDAP podem utilizar entryUUID ou algo semelhante.  A predefinição é objectGUID. |
| Tipo de identificador exclusivo |Selecione o tipo do atributo de identificador exclusivo.  No Active Directory, o atributo objectGUID é do tipo GUID. Outras implementações de LDAP podem utilizar o tipo Matriz de Bytes ASCII ou Cadeia.  A predefinição é GUID. <br><br>É importante definir este tipo corretamente, pois os Itens de Sincronização são referenciados pelos Identificadores Exclusivos. O Tipo de Identificador Exclusivo é utilizado para localizar diretamente o objeto no diretório.  Definir este tipo como Cadeia quando, na verdade, o diretório armazena o valor como uma matriz de bytes de carateres ASCII impede que a sincronização funcione corretamente. |
| Nome único |Introduza o nome de atributo do atributo que contém o nome único para cada registo.  No Active Directory, normalmente é distinguishedName. Outras implementações de LDAP podem utilizar entryDN ou algo semelhante.  A predefinição é distinguishedName. <br><br>Se não existir um atributo que contém o nome único, pode ser utilizado o atributo de caminho de anúncios.  A parte "LDAP://\<server\>/" do caminho é removida automaticamente, deixando apenas o nome único do objeto. |
| Nome do contentor |Introduza o nome de atributo do atributo que contém o nome num registo de contentor.  O valor deste atributo é apresentado na Hierarquia de Contentores ao importar a partir do Active Directory ou ao adicionar itens de sincronização.  A predefinição é name. <br><br>Se contentores diferentes utilizarem atributos diferentes para os respetivos nomes, separe os vários atributos de nomes de contentores por ponto e vírgula.  O primeiro atributo de nome de contentor encontrado num objeto de contentor é utilizado para apresentar o respetivo nome. |
| Nome do grupo de segurança |Introduza o nome de atributo do atributo que contém o nome num registo de grupo de segurança.  O valor deste atributo é apresentado na lista Grupos de Segurança ao importar a partir do Active Directory ou ao adicionar itens de sincronização.  A predefinição é name. |
| Nome de utilizador |Introduza o nome de atributo do atributo que contém o nome de utilizador num registo de utilizador.  O valor deste atributo é utilizado como o nome de utilizador do Servidor Multi-Factor Auth.  Um segundo atributo pode ser especificado como cópia de segurança do primeiro.  O segundo atributo só é utilizado se o primeiro não incluir um valor para o utilizador.  As predefinições são userPrincipalName e sAMAccountName. |
| Nome próprio |Introduza o nome de atributo do atributo que contém o nome próprio num registo de utilizador.  A predefinição é givenName. |
| Apelido |Introduza o nome de atributo do atributo que contém o apelido num registo de utilizador.  A predefinição é sn. |
| Endereço de e-mail |Introduza o nome de atributo do atributo que contém o endereço de e-mail num registo de utilizador.  O endereço de e-mail é utilizado para enviar mensagens de e-mail de boas-vindas e de atualização ao utilizador.  A predefinição é mail. |
| Grupo de utilizadores |Introduza o nome de atributo do atributo que contém o grupo de utilizadores num registo de utilizador.  O grupo de utilizadores pode ser utilizado para filtrar os utilizadores no agente e em relatórios no Portal de Gestão do Servidor Multi-Factor Auth. |
| Descrição |Introduza o nome de atributo do atributo que contém a descrição num registo de utilizador.  A descrição só é utilizada para procurar.  A predefinição é description. |
| Idioma da chamada telefónica |Introduza o nome de atributo do atributo que contém o nome abreviado do idioma a utilizar para chamadas de voz do utilizador. |
| Idioma da mensagem de texto |Introduza o nome de atributo do atributo que contém o nome abreviado do idioma a utilizar para mensagens de texto SMS do utilizador. |
| Idioma da aplicação móvel |Introduza o nome de atributo do atributo que contém o nome abreviado do idioma a utilizar para mensagens de texto da aplicação para telemóvel do utilizador. |
| Idioma do token OATH |Introduza o nome de atributo do atributo que contém o nome abreviado do idioma a utilizar para mensagens de texto do token OATH do utilizador. |
| Telefone da empresa |Introduza o nome de atributo do atributo que contém o número de telefone da empresa num registo de utilizador.  A predefinição é telephoneNumber. |
| Telefone de casa |Introduza o nome de atributo do atributo que contém o número de telefone de casa num registo de utilizador.  A predefinição é homePhone. |
| Pager |Introduza o nome de atributo do atributo que contém o número de pager num registo de utilizador.  A predefinição é pager. |
| Número de telemóvel |Introduza o nome de atributo do atributo que contém o número de telemóvel num registo de utilizador.  A predefinição é mobile. |
| Fax |Introduza o nome de atributo do atributo que contém o número de fax num registo de utilizador.  A predefinição é facsimileTelephoneNumber. |
| Número de IP |Introduza o nome de atributo do atributo que contém o número de telefone de IP num registo de utilizador.  A predefinição é ipPhone. |
| Personalizado |Introduza o nome de atributo do atributo que contém um número de telefone personalizado num registo de utilizador.  A predefinição é blank. |
| Extensão |Introduza o nome de atributo do atributo que contém a extensão de número de telemóvel num registo de utilizador.  O valor do campo de extensão é utilizado como a extensão apenas do número de telefone principal.  A predefinição é blank. <br><br>Se o atributo Extensão não for especificado, as extensões podem ser incluídas como parte do atributo de telefone. Neste caso, preceda a extensão com um “x”, para que seja analisada corretamente.  Por exemplo, 555-123-4567 x890 resultaria em 555-123-4567 como o número de telefone e 890 como a extensão. |
| Botão Restaurar Predefinições |Clique no botão **Restaurar Predefinições** para voltar a colocar todos os atributos nos valores predefinidos.  As predefinições devem funcionar corretamente com o esquema normal do Active Directory ou do ADAM. |

Para editar os atributos, clique em **Editar**, no separador Atributos.  É aberta uma janela onde pode editá-los. Selecione **...** junto a qualquer atributo para abrir uma janela onde pode escolher que atributos mostrar.

![Editar mapeamento do atributo de diretório no servidor MFA](./media/howto-mfaserver-dir-ad/dirint4.png)

## <a name="synchronization"></a>Sincronização

A sincronização mantém a base de dados de utilizadores do MFA do Azure sincronizada com os utilizadores do Active Directory ou de outro diretório de protocolo LDAP (Lightweight Directory Access Protocol). O processo é semelhante à importação manual de utilizadores do Active Directory, mas, periodicamente, consulta alterações ao grupo de segurança e ao utilizador do Active Directory para processar.  Também desativa ou remove os utilizadores que foram removidos de um contentor, de um grupo de segurança ou do Active Directory.

O serviço ADSync do Multi-Factor Auth é um serviço Windows que executa a consulta periódica do Active Directory.  Não deve ser confundido com o Azure AD Sync ou o Azure AD Connect.  O ADSync do Multi-Factor Auth, embora incorporado numa base de código semelhante, é específico do Servidor Multi-Factor Authentication do Azure.  É instalado num estado parado e é iniciado pelo serviço do Servidor Multi-Factor Auth quando configurado para execução.  Se tiver uma configuração de Servidor Multi-Factor Auth multisservidor, o ADSync do Multi-Factor Auth só pode ser executado num único servidor.

O serviço ADSync do Multi-Factor Auth utiliza a extensão de servidor LDAP DirSync fornecida pela Microsoft para consultar alterações de forma eficiente.  Este chamador de controlo DirSync tem de ter o direito "directory get changes" e o direito de acesso de controlo expandido DS-Replication-Get-Changes.  Por predefinição, estes direitos são atribuídos às contas de Administrador e LocalSystem nos controladores de domínio.  O serviço AdSync do Multi-Factor Auth está configurado para ser executado como LocalSystem, por predefinição.  Por isso, é mais simples executar o serviço num controlador de domínio.  Se configurar o serviço para fazer sempre uma sincronização completa, este pode ser executado como uma conta com permissões menores.  É menos eficiente, mas requer menos privilégios de conta.

Se o diretório LDAP suportar e estiver configurado para DirSync, a consulta de alterações a grupos de segurança e utilizadores funcionará da mesma forma que funciona com o Active Directory.  Se o diretório LDAP não suportar o controlo DirSync, será executada uma sincronização completa durante cada ciclo.

![Sincronização de objetos de diretório para o servidor MFA](./media/howto-mfaserver-dir-ad/dirint5.png)

A tabela seguinte contém informações adicionais sobre cada uma das definições do separador Sincronização.

| Funcionalidade | Descrição |
| --- | --- |
| Ativar a sincronização com o Active Directory |Quando selecionada, o serviço Servidor Multi-Factor Auth consulta periodicamente alterações no Active Directory. <br><br>Nota: Tem de ser adicionado, pelo menos, um Item de sincronização e sincronizar agora tem de ser realizada antes do serviço de servidor do multi-factor Auth será iniciado o processamento de alterações. |
| Sincronizar a cada |Especifique o intervalo de tempo que o serviço Servidor Multi-Factor Auth irá esperar entre consultas e processamento de alterações. <br><br> Nota: O intervalo especificado é o tempo entre o início de cada ciclo.  Se o tempo de processamento de alterações exceder o intervalo, o serviço voltará a consultar de imediato. |
| Remover utilizadores que já não constem no Active Directory |Quando selecionada, o serviço Servidor Multi-Factor Auth irá processar tombstones de utilizadores eliminados do Active Directory e remover o utilizador do Servidor Multi-Factor Auth relacionado. |
| Efetuar sempre uma sincronização completa |Quando selecionada, o serviço Servidor Multi-Factor Auth irá efetuar sempre uma sincronização completa.  Quando não estiver selecionada, o serviço Servidor Multi-Factor Auth irá efetuar uma sincronização incremental, consultando apenas os utilizadores que tenham sido alterados.  A predefinição é a caixa desmarcada. <br><br>Quando desmarcada, o Servidor da MFA do Azure só faz uma sincronização incremental quando o diretório suportar o controlo DirSync e a conta que está a ser vinculada ao diretório tiver as permissões para fazer consultas incrementais de DirSync.  Se a conta não tiver as permissões adequadas ou vários domínios estiverem envolvidos na sincronização, o Servidor do MFA do Azure faz uma sincronização completa. |
| Exigir aprovação do administrador quando o número de utilizadores desativados ou removidos exceder o limiar |Os itens de sincronização podem ser configurados para desativar ou remover utilizadores que já não são membros do grupo de segurança ou do contentor do item.  Como salvaguarda, a aprovação do administrador pode ser exigida quando o número de utilizadores a desativar ou a remover exceder um limiar.  Quando selecionada, é exigida aprovação para o limiar especificado.  A predefinição é 5 e o intervalo é de 1 a 999. <br><br> A aprovação é facilitada ao enviar primeiro uma notificação por e-mail aos administradores. A notificação por e-mail dá instruções para rever e aprovar a desativação e remoção de utilizadores.  Quando a interface de utilizador do Servidor Multi-Factor Auth for iniciada, pedirá aprovação. |

O botão **Sincronizar Agora** permite executar uma sincronização completa para os itens de sincronização especificados.  Uma sincronização completa é precisa sempre que forem adicionados, modificados, removidos ou reordenados itens de sincronização.  Também é precisa para que o serviço AdSync do Multi-Factor Auth fique operacional, uma vez que define o ponto de partida a partir do qual o serviço irá consultar as alterações incrementais.  Se tiverem sido feitas alterações aos itens de sincronização, mas não tiver sido efetuada uma sincronização completa, é-lhe pedido para Sincronizar Agora.

O botão **Remover** permite ao administrador eliminar um ou mais itens de sincronização da lista de itens de sincronização do Servidor Multi-Factor Auth.

> [!WARNING]
> Assim que um registo de item de sincronização tiver sido removido, não pode ser recuperado. Terá de voltar a adicionar o registo do item de sincronização, caso o tenha eliminado por engano.

O item de sincronização ou os itens de sincronização foram removidos do Servidor Multi-Factor Auth.  O serviço Servidor Multi-Factor Auth já não irá processar os itens de sincronização.

Os botões Mover para cima e Mover para baixo permitem ao administrador alterar a ordem dos itens de sincronização.  A ordem é importante, uma vez que o mesmo utilizador poderá ser membro de mais do que um item de sincronização (por exemplo, um contentor e um grupo de segurança).  As definições aplicadas ao utilizador durante a sincronização serão provenientes do primeiro item de sincronização na lista ao qual o utilizador está associado.  Por isso, os itens de sincronização devem ser colocados por ordem de prioridade.

> [!TIP]
> Depois de remover itens de sincronização, deve fazer uma sincronização completa.  Depois de ordenar itens de sincronização, deve fazer uma sincronização completa.  Clique em **Sincronizar Agora** para fazer uma sincronização completa.

## <a name="multi-factor-authentication-servers"></a>Servidores multi-factor Authentication

Servidores de multi-factor Authentication adicionais podem ser configurados para servir como um proxy RADIUS de cópia de segurança, LDAP proxy, ou para autenticação do IIS. A configuração da Sincronização é partilhada entre todos os agentes. No entanto, apenas um destes agentes poderá ter a execução de serviço do servidor multi-factor Authentication. Este separador permite-lhe selecionar o servidor multi-factor Authentication que deve ser ativado para sincronização.

![Relacionados com servidores multi-factor Authentication](./media/howto-mfaserver-dir-ad/dirint6.png)
