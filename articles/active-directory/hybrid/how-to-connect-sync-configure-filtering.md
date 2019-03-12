---
title: 'Sincronização do Azure AD Connect: Configurar a filtragem | Documentos da Microsoft'
description: Explica como configurar a filtragem de sincronização do Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 880facf6-1192-40e9-8181-544c0759d506
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53c14ce92a422c2254a1e9b7fc4989b49790a88a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57774443"
---
# <a name="azure-ad-connect-sync-configure-filtering"></a>Sincronização do Azure AD Connect: Configurar a filtragem
Ao utilizar a filtragem, pode controlar quais os objetos são apresentados no Azure Active Directory (Azure AD) do seu diretório no local. A configuração predefinida leva todos os objetos em todos os domínios nas florestas configurados. Em geral, esta é a configuração recomendada. Os utilizadores que utilizam a cargas de trabalho do Office 365, como o Exchange Online e Skype para empresas, beneficiam de uma lista completa de endereço Global para que possam enviar e-mail e chamar todos os utilizadores. Com a configuração predefinida, que é preciso a mesma experiência que eles teriam com uma implementação no local do Exchange ou o Lync.

Em alguns casos, no entanto, é necessário fazer algumas alterações à configuração padrão. Eis alguns exemplos:

* Planeja usar o [topologia de diretório com vários Azure AD](plan-connect-topologies.md#each-object-only-once-in-an-azure-ad-tenant). Em seguida, tem de aplicar um filtro para controlar quais os objetos que são sincronizados a um determinado diretório do Azure AD.
* Executar um piloto para o Azure ou do Office 365 e quiser apenas um subconjunto de utilizadores no Azure AD. No projeto piloto pequenas, não é importante ter uma lista de endereços Global completa para demonstrar a funcionalidade.
* Tem muitas contas de serviço e outras contas nonpersonal que não quer no Azure AD.
* Por motivos de conformidade, não elimine nenhum utilizador contas no local. Apenas desativá-las. Mas, no Azure AD, apenas contas ativas para estar presente.

Este artigo explica como configurar os diferentes métodos de filtragem.

> [!IMPORTANT]
> A Microsoft não suporta a modificação ou operação de sincronizações do Azure AD Connect fora das ações anteriormente documentadas. Qualquer destas ações pode resultar num estado inconsistente ou não suportado da sincronização do Azure AD Connect. Como resultado, a Microsoft não pode possível fornecer o suporte técnico para implementações deste tipo.

## <a name="basics-and-important-notes"></a>Noções básicas e notas importantes
No Azure AD Connect sync, pode ativar a filtragem em qualquer altura. Se começar com uma configuração predefinida de sincronização de diretórios e, em seguida, configurar a filtragem, os objetos que são filtrados já não são sincronizados com o Azure AD. Por causa dessa alteração, todos os objetos no Azure AD que anteriormente foram sincronizados, mas foram filtrados, em seguida, são eliminados no Azure AD.

Antes de começar a fazer alterações à filtragem, certifique-se de que [desativar a tarefa agendada](#disable-the-scheduled-task) para que não exporta acidentalmente as alterações que ainda não tiver verificado esteja correto.

Uma vez filtragem, pode remover muitos objetos ao mesmo tempo, deseja certificar-se de que os novos filtros estão corretos antes de começar a exportar todas as alterações para o Azure AD. Depois de concluir os passos de configuração, recomendamos vivamente que siga o [passos de verificação](#apply-and-verify-changes) antes de exportar e fazer alterações para o Azure AD.

Para proteção contra a eliminação de vários objetos por acidente, a funcionalidade "[impedir eliminações acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md)" está ativada por predefinição. Se eliminar muitos objetos devido a filtragem (500 por predefinição), tem de seguir os passos neste artigo para permitir que as eliminações percorrer para o Azure AD.

Se usar uma compilação antes de Novembro de 2015 ([1.0.9125](reference-connect-version-history.md#1091250)), fazer uma alteração para uma configuração de filtro e utilizar a sincronização de hash de palavra-passe, tem de acionar uma sincronização completa de todas as senhas, depois de concluir a configuração. Para obter passos sobre como acionar uma sincronização completa de palavra-passe, consulte [acionar uma sincronização completa de todas as senhas](tshoot-connect-password-hash-synchronization.md#trigger-a-full-sync-of-all-passwords). Se estiver a compilação 1.0.9125 ou posterior, em seguida, regular **completa sincronização** ação também calcula se devem ser sincronizadas as palavras-passe e se essa etapa adicional já não é necessária.

Se **utilizador** objetos foram inadvertidamente eliminados no Azure AD devido a um erro de filtragem, pode recriar os objetos de utilizador no Azure AD, removendo suas configurações de filtragem. Em seguida, pode sincronizar os diretórios novamente. Esta ação restaura os utilizadores da lixeira no Azure AD. No entanto, não é possível anular outros tipos de objeto. Por exemplo, se excluir acidentalmente um grupo de segurança e ela era usada para a ACL de um recurso, o grupo e respetivas ACLs não podem ser recuperadas.

O Azure AD Connect elimina apenas os objetos que tem uma vez não pudesse para ser no âmbito. Se há objetos no Azure AD que foram criados por outro motor de sincronização e esses objetos não estão no âmbito, adicionar filtragem não removê-los. Por exemplo, se começar com um servidor do DirSync que criou uma cópia completa do seu diretório todo no Azure AD e instalar um novo servidor de sincronização do Azure AD Connect em paralelo com a filtragem ativada desde o início, o Azure AD Connect não remove os objetos adicionais que são criados pelo DirSync.

A configuração de filtragem é retida ao instalar ou atualizar para uma versão mais recente do Azure AD Connect. É sempre uma prática recomendada, certifique-se de que a configuração não era inadvertidamente alterada após uma atualização para uma versão mais recente antes de executar primeiro ciclo de sincronização.

Se tiver mais do que uma floresta, tem de aplicar as configurações de filtragem que são descritas neste tópico para todas as florestas (partindo do princípio de que pretende que a mesma configuração para todos eles).

### <a name="disable-the-scheduled-task"></a>Desativar a tarefa agendada
Para desativar o agendador interno que aciona um ciclo de sincronização a cada 30 minutos, siga estes passos:

1. Vá para o PowerShell prompt.
2. Executar `Set-ADSyncScheduler -SyncCycleEnabled $False` para desativar o scheduler.
3. Faça as alterações que estão documentadas neste artigo.
4. Executar `Set-ADSyncScheduler -SyncCycleEnabled $True` para ativar o agendador novamente.

**Se usar uma compilação do Azure AD Connect antes de 1.1.105.0**  
Para desativar a tarefa agendada que aciona um ciclo de sincronização a cada três horas, siga estes passos:

1. Iniciar **agendador de tarefas** partir do **iniciar** menu.
2. Diretamente sob **biblioteca do Programador de tarefas**, localize a tarefa com o nome **agendador do Azure AD Sync**, botão direito do mouse e selecione **desativar**.  
   ![Agendador de tarefas](./media/how-to-connect-sync-configure-filtering/taskscheduler.png)  
3. Agora pode efetuar alterações de configuração e executar o motor de sincronização manualmente a partir da **Synchronization Service Manager** consola.

Depois de concluir todas as alterações de filtragem, não se esqueça de voltar atrás e **ativar** novamente a tarefa.

## <a name="filtering-options"></a>Opções de filtragem
Pode aplicar os seguintes tipos de configuração de filtragem para a ferramenta de sincronização de diretórios:

* [**Com base em grupo**](#group-based-filtering): Filtragem com base num único grupo só pode ser configurada na instalação inicial, utilizando o Assistente de instalação.
* [**Com base em domínio**](#domain-based-filtering): Ao utilizar esta opção, pode selecionar quais os domínios que sincronizar com o Azure AD. Também pode adicionar e remover domínios da configuração do motor de sincronização quando fizer alterações à sua infraestrutura no local depois de instalar a sincronização do Azure AD Connect.
* [**Unidade organizacional (UO) – com base**](#organizational-unitbased-filtering): Ao utilizar esta opção, pode selecionar qual UOs sincronizar com o Azure AD. Esta opção destina-se a todos os tipos de objetos na UO selecionado.
* [**Baseadas em atributos**](#attribute-based-filtering): Ao utilizar esta opção, pode filtrar os objetos com base nos valores de atributo nos objetos. Também pode ter diferentes filtros para diferentes tipos de objeto.

Pode utilizar várias opções de filtragem ao mesmo tempo. Por exemplo, pode utilizar a filtragem baseada em UO para incluir apenas os objetos numa unidade Organizacional. Ao mesmo tempo, pode utilizar a filtragem baseada em atributo para filtrar ainda mais os objetos. Quando usa vários métodos de filtragem, os filtros de usar um "e" lógico entre os filtros.

## <a name="domain-based-filtering"></a>Filtragem baseada em domínio
Esta secção fornece os passos para configurar o filtro de domínio. Se adicionou ou removeu domínios na sua floresta, após a instalação do Azure AD Connect, também tem de atualizar a configuração de filtragem.

A maneira preferencial para alterar a filtragem baseada em domínio é executar o Assistente de instalação e alterando [domínio e a filtragem de UO](how-to-connect-install-custom.md#domain-and-ou-filtering). O Assistente de instalação automatiza a todas as tarefas que estão documentadas neste tópico.

Apenas deve seguir estes passos, se não for possível executar o Assistente de instalação por algum motivo.

Configuração de filtragem baseada em domínio é composta por estes passos:

1. Selecione os domínios que pretende incluir na sincronização.
2. Para cada domínio adicionado e removido, ajuste os perfis de execução.
3. [Aplicar e verificar as alterações](#apply-and-verify-changes).

### <a name="select-the-domains-to-be-synchronized"></a>Selecione os domínios a serem sincronizados
Para definir o filtro de domínio, siga os passos abaixo:

1. Inicie sessão no servidor que está em execução do Azure AD Connect, utilizando uma conta que seja membro do **ADSyncAdmins** grupo de segurança.
2. Iniciar **serviço de sincronização** partir a **iniciar** menu.
3. Selecione **conectores**e, no **conectores** , selecione o conector com o tipo **serviços de domínio do Active Directory**. Na **ações**, selecione **propriedades**.  
   ![Propriedades do conector](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Clique em **configurar partições de diretório**.
5. Na **selecionar partições de diretório** lista, selecione e anule a seleção de domínios conforme necessário. Certifique-se de que apenas as partições que pretende sincronizar estão selecionadas.  
   ![Partições](./media/how-to-connect-sync-configure-filtering/connectorpartitions.png)  
   Se tiver alterado sua infraestrutura de Active Directory no local e adicionados ou removidos domínios da floresta de, em seguida, clique nas **atualizar** botão para obter uma lista atualizada. Quando atualiza, lhe forem pedidas credenciais. Fornece nenhuma credencial com acesso de leitura ao Windows Server Active Directory. Não tem de ser o utilizador pré-populada na caixa de diálogo.  
   ![Atualização necessária](./media/how-to-connect-sync-configure-filtering/refreshneeded.png)  
6. Quando tiver terminado, feche o **propriedades** caixa de diálogo clicando **OK**. Se removeu domínios da floresta, uma mensagem de pop-up diz que foi removido um domínio e que a configuração serão limpos.
7. Continue ajustar os perfis de execução.

### <a name="update-the-run-profiles"></a>Os perfis de execução de atualização
Se atualizou seu filtro de domínio, terá também de atualizar os perfis de execução.

1. Na **conectores** lista, certifique-se de que o conector que alterou no passo anterior está selecionado. Na **ações**, selecione **configurar perfis de execução**.  
   ![Conector de 1 de perfis de execução](./media/how-to-connect-sync-configure-filtering/connectorrunprofiles1.png)  
2. Localizar e identificar os seguintes perfis:
    * Importação completa
    * Sincronização completa
    * Importação delta
    * Sincronização delta
    * Exportar
3. Para cada perfil, ajustar a **adicionados** e **removido** domínios.
    1. Para cada um dos perfis de cinco, siga os passos abaixo para cada **adicionado** domínio:
        1. Selecione o perfil de execução e clique em **novo passo**.
        2. No **passo configurar** página, além da **tipo** menu pendente, selecione o tipo do passo com o mesmo nome que o perfil que está a configurar. Clique depois em **Seguinte**.  
        ![Conector de 2 de perfis de execução](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep1.png)  
        3. No **configuração do conector** página, além do **partição** menu pendente, selecione o nome do domínio que adicionou ao seu filtro de domínio.  
        ![Conector 3 de perfis de execução](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep2.png)  
        4. Para fechar a **configurar perfil de execução** caixa de diálogo, clique em **concluir**.
    2. Para cada um dos perfis de cinco, siga os passos abaixo para cada **removido** domínio:
        1. Selecione o perfil de execução.
        2. Se o **valor** da **partição** atributo é um GUID, selecione o passo de execução e clique em **passo eliminar**.  
        ![4 de perfis de execução do conector](./media/how-to-connect-sync-configure-filtering/runprofilesdeletestep.png)  
    3. Certifique-se a sua alteração. Cada domínio que pretende sincronizar deve estar listado como um passo em cada perfil de execução.
4. Para fechar a **configurar perfis de execução** caixa de diálogo, clique em **OK**.
5.  Para concluir a configuração, tem de executar uma **importação completa** e uma **sincronização Delta**. Continue a ler a secção [aplicar e verificar as alterações](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Baseado em unidade organizacional filtragem
A maneira preferencial para alterar a filtragem baseada em UO é executar o Assistente de instalação e alterando [domínio e a filtragem de UO](how-to-connect-install-custom.md#domain-and-ou-filtering). O Assistente de instalação automatiza a todas as tarefas que estão documentadas neste tópico.

Apenas deve seguir estes passos, se não for possível executar o Assistente de instalação por algum motivo.

Para configurar a baseado em unidade organizacional filtragem, siga os passos abaixo:

1. Inicie sessão no servidor que está em execução do Azure AD Connect, utilizando uma conta que seja membro do **ADSyncAdmins** grupo de segurança.
2. Iniciar **serviço de sincronização** partir a **iniciar** menu.
3. Selecione **conectores**e, no **conectores** , selecione o conector com o tipo **serviços de domínio do Active Directory**. Na **ações**, selecione **propriedades**.  
   ![Propriedades do conector](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Clique em **configurar partições de diretório**, selecione o domínio que pretende configurar e, em seguida, clique em **contentores**.
5. Quando lhe for pedido, forneça credenciais com acesso de leitura para o Active Directory no local. Não tem de ser o utilizador pré-populada na caixa de diálogo.
6. Na **selecionar contentores** caixa de diálogo caixa, desmarque as UOs que não pretende sincronizar com o diretório em nuvem e, em seguida, clique em **OK**.  
   ![UOs na caixa de diálogo Selecionar contentores](./media/how-to-connect-sync-configure-filtering/ou.png)  
   * O **computadores** contentor deve estar selecionado para os seus computadores Windows 10 sejam sincronizadas com êxito para o Azure AD. Se os computadores associados a um domínio estiverem localizados em outras unidades organizacionais, certificar-se de que os estão selecionados.
   * O contentor **ForeignSecurityPrincipals** deverá ser selecionado, se tiver várias florestas com confianças. Este contentor permite resolver a associação ao grupo de segurança de múltiplas florestas.
   * O **RegisteredDevices** UO deve ser selecionado se ativou a funcionalidade de repetição de escrita do dispositivo. Se utilizar outro recurso de repetição de escrita, por exemplo, a repetição de escrita do grupo, certifique-se destas que localizações estão selecionadas.
   * Selecione qualquer outra UO onde estão localizados os utilizadores, iNetOrgPersons, grupos, contactos e computadores. Na imagem, todos esses UOs estão localizadas na UO ManagedObjects.
   * Se utilizar a filtragem baseada em grupo, em seguida, a UO onde está localizado o grupo tem de ser incluída.
   * Tenha em atenção que pode configurar se as novas UOs adicionadas depois de concluída a configuração de filtragem são sincronizadas ou não sincronizadas. Veja a secção seguinte para obter detalhes.
7. Quando tiver terminado, feche o **propriedades** caixa de diálogo clicando **OK**.
8. Para concluir a configuração, tem de executar uma **importação completa** e uma **sincronização Delta**. Continue a ler a secção [aplicar e verificar as alterações](#apply-and-verify-changes).

### <a name="synchronize-new-ous"></a>Sincronizar as novas UOs
As novas UOs criadas após a filtragem tiver sido configurada são sincronizadas por predefinição. Este estado é indicado por uma caixa de verificação selecionada. Também pode anular a seleção de alguns subunidades organizacionais. Para obter esse comportamento, clique na caixa até que ele se torne branco com uma marca de verificação (seu estado padrão) azul. Em seguida, anule a seleção de todas as subunidades organizacionais que não pretende sincronizar.

Se todas as subunidades organizacionais são sincronizadas, o box é branco com uma marca de verificação azul.  
![UO com todas as caixas selecionadas](./media/how-to-connect-sync-configure-filtering/ousyncnewall.png)

Se algum subunidades organizacionais tiverem sido não selecionadas, em seguida, a caixa está cinza com uma marca de verificação branca.  
![UO com alguns subunidades organizacionais desmarcada](./media/how-to-connect-sync-configure-filtering/ousyncnew.png)

Com esta configuração, uma nova UO que foi criada sob ManagedObjects é sincronizada.

O Assistente de instalação do Azure AD Connect cria sempre esta configuração.

### <a name="dont-synchronize-new-ous"></a>Não sincronizar as novas UOs
Pode configurar o motor de sincronização não sincronizar as novas UOs após concluir a configuração de filtragem. Este estado é indicado na interface do Usuário, a caixa que aparece a cinzento sólido com nenhuma marca de verificação. Para obter esse comportamento, clique na caixa até que ele se torne branco com nenhuma marca de verificação. Em seguida, selecione as subunidades organizacionais que pretende sincronizar.

![UO com a raiz desmarcada](./media/how-to-connect-sync-configure-filtering/oudonotsyncnew.png)

Com esta configuração, uma nova UO que foi criada sob ManagedObjects não está sincronizada.

## <a name="attribute-based-filtering"></a>Filtragem baseada em atributo
Certifique-se de que está a utilizar a Novembro de 2015 ([1.0.9125](reference-connect-version-history.md#1091250)) ou criar mais tarde para estes passos para trabalhar.

> [!IMPORTANT]
>A Microsoft recomenda para não modificar as regras predefinidas criadas pelo **do Azure AD Connect**. Se pretender modificar a regra, em seguida, clone-o e desativar a regra original. Efetue as alterações para a regra clonada. Tenha em atenção que ao fazer isso (a desativar a regra original) irá perder quaisquer correções de erros ou recursos habilitados por meio dessa regra.

Filtragem baseada em atributo é a maneira mais flexível de objetos de filtro. Pode utilizar o poder da [aprovisionamento declarativo](concept-azure-ad-connect-sync-declarative-provisioning.md) para controlar quase todos os aspectos do quando um objeto está sincronizado com o Azure AD.

Pode aplicar [entrada](#inbound-filtering) filtragem do Active Directory para o metaverso, e [saída](#outbound-filtering) filtragem do metaverse, para o Azure AD. Recomendamos que aplique a filtragem de entrada porque esse é o mais fácil de manter. Só deve utilizar a filtragem de saída se é necessário para associar objetos a partir de mais do que uma floresta antes da avaliação pode ser feitas.

### <a name="inbound-filtering"></a>Filtragem de entrada
Filtragem de entrada utiliza a configuração predefinida, onde objetos vai para o Azure AD têm de ter o cloudFiltered de atributo do metaverso não definido como um valor a ser sincronizados. Se o valor desse atributo estiver definido como **True**, em seguida, o objeto não está sincronizado. Ele não deve ser definido como **False**, por design. Para certificar-se de que outras regras têm a capacidade de contribuir com um valor, esse atributo apenas deve para ter os valores **True** ou **nulo** (ausente).

Filtragem de entrada, vai utilizar o poder da **âmbito** para determinar quais os objetos a sincronizar ou não a sincronização. Isso é onde fazer ajustes para satisfazer as necessidades da sua organização. O módulo de âmbito tem um **grupo** e uma **cláusula** para determinar quando uma regra de sincronização está no âmbito. Um grupo contém uma ou mais cláusulas. Existe um "e" lógico entre várias cláusulas e um "ou" lógico entre vários grupos.

Vamos examinar um exemplo:  
![Âmbito](./media/how-to-connect-sync-configure-filtering/scope.png)  
Isso deve ser lida como **(departamento = IT) ou (departamento = Vendas e c = US)**.

Nos seguintes exemplos e passos, utilize o objeto de utilizador como um exemplo, mas pode utilizá-la para todos os tipos de objeto.

Nos exemplos a seguir, o valor de precedência começa com 50. Isso pode ser qualquer número não utilizado, mas deve ser inferior a 100.

#### <a name="negative-filtering-do-not-sync-these"></a>Filtragem negativo: "não são sincronizados estes"
No exemplo a seguir, filtrar (não sincronizar) todos os utilizadores onde **extensionAttribute15** tem o valor **NoSync**.

1. Inicie sessão no servidor que está em execução do Azure AD Connect, utilizando uma conta que seja membro do **ADSyncAdmins** grupo de segurança.
2. Iniciar **Editor de regras de sincronização** partir do **iniciar** menu.
3. Certifique-se **Inbound** está selecionada e clique em **Adicionar nova regra**.
4. Dê um nome descritivo, a regra, tal como "*do AD – utilizador DoNotSyncFilter*". Selecione a floresta correta, selecione **usuário** como o **tipo de objeto do CS**e selecione **pessoa** como o **tipo de objeto de MV**. Na **tipo de ligação**, selecione **associar**. Na **precedência**, escreva um valor que não está atualmente utilizado por outra regra de sincronização (por exemplo, 50) e, em seguida, clique em **próxima**.  
   ![1 Descrição de entrada](./media/how-to-connect-sync-configure-filtering/inbound1.png)  
5. Na **filtro Scoping**, clique em **adicionar grupo**e clique em **Adicionar cláusula**. Na **atributo**, selecione **ExtensionAttribute15**. Certifique-se de que **operador** está definida como **igual**e digite o valor **NoSync** no **valor** caixa. Clique em **Seguinte**.  
   ![Âmbito de 2 de entrada](./media/how-to-connect-sync-configure-filtering/inbound2.png)  
6. Deixe o **aderir** regras vazia e, em seguida, clique em **próxima**.
7. Clique em **adicionar transformação**, selecione a **FlowType** como **constante**e selecione **cloudFiltered** como o **destino Atributo**. Na **origem** caixa de texto, escreva **verdadeiro**. Clique em **adicionar** para guardar a regra.  
   ![3 de transformação de entrada](./media/how-to-connect-sync-configure-filtering/inbound3.png)
8. Para concluir a configuração, tem de executar uma **Full sync**. Continue a ler a secção [aplicar e verificar as alterações](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Filtragem positiva: "apenas sincronizar essas"
Expressar a filtragem positivo pode ser mais desafiador porque tem também a serem considerados objetos que não ficam evidentes para serem sincronizadas, como salas de conferência. Também vai substituir o filtro predefinido na regra de out-of-box **do AD - associação de utilizador**. Quando cria seu filtro personalizado, lembre-se de que não incluem os objetos de sistema crítico, objetos de conflito de replicação, caixas de correio especiais e as contas de serviço do Azure AD Connect.

A opção de filtragem positiva requer duas regras de sincronização. Precisa de uma regra (ou várias) com o âmbito correto de objetos a sincronizar. Também precisa de uma segunda regra de sincronização de catch-all, que filtra todos os objetos que ainda não tiverem sido identificados como um objeto que deve ser sincronizado.

No exemplo a seguir, sincronizar apenas os objetos de utilizador em que o atributo department tem o valor **vendas**.

1. Inicie sessão no servidor que está em execução do Azure AD Connect, utilizando uma conta que seja membro do **ADSyncAdmins** grupo de segurança.
2. Iniciar **Editor de regras de sincronização** partir do **iniciar** menu.
3. Certifique-se **Inbound** está selecionada e clique em **Adicionar nova regra**.
4. Dê um nome descritivo, a regra, tal como "*do AD – utilizador vendas sincronizar*". Selecione a floresta correta, selecione **usuário** como o **tipo de objeto do CS**e selecione **pessoa** como o **tipo de objeto de MV**. Na **tipo de ligação**, selecione **associar**. Na **precedência**, escreva um valor que não está atualmente utilizado por outra regra de sincronização (por exemplo, 51) e, em seguida, clique em **próxima**.  
   ![Descrição de 4 de entrada](./media/how-to-connect-sync-configure-filtering/inbound4.png)  
5. Na **filtro Scoping**, clique em **adicionar grupo**e clique em **Adicionar cláusula**. Na **atributo**, selecione **departamento**. Certifique-se de que o operador estiver definido como **igual**e digite o valor **vendas** no **valor** caixa. Clique em **Seguinte**.  
   ![Âmbito de 5 de entrada](./media/how-to-connect-sync-configure-filtering/inbound5.png)  
6. Deixe o **aderir** regras vazia e, em seguida, clique em **próxima**.
7. Clique em **adicionar transformação**, selecione **constante** como o **FlowType**e selecione o **cloudFiltered** como o **destino Atributo**. Na **origem** , escreva **falso**. Clique em **adicionar** para guardar a regra.  
   ![6 de transformação de entrada](./media/how-to-connect-sync-configure-filtering/inbound6.png)  
   Este é um caso especial em que defina explicitamente cloudFiltered para **False**.
8. Agora, temos de criar a regra de sincronização de catch-all. Dê um nome descritivo, a regra, tal como "*do AD – filtro de utilizador Catch-all*". Selecione a floresta correta, selecione **usuário** como o **tipo de objeto do CS**e selecione **pessoa** como o **tipo de objeto de MV**. Na **tipo de ligação**, selecione **associar**. Na **precedência**, escreva um valor que não está atualmente utilizado por outra regra de sincronização (por exemplo, 99). Selecionou um valor de precedência superior (precedência inferior) que a regra de sincronização anterior. Mas também Deixei algum espaço para que pode adicionar mais regras de sincronização de filtragem mais tarde, quando deseja iniciar a sincronização departamentos adicionais. Clique em **Seguinte**.  
   ![Descrição de 7 de entrada](./media/how-to-connect-sync-configure-filtering/inbound7.png)  
9. Deixe **filtro Scoping** vazio e clique em **próxima**. Um filtro vazio indica que a regra está a ser aplicado a todos os objetos.
10. Deixe o **aderir** regras vazia e, em seguida, clique em **próxima**.
11. Clique em **adicionar transformação**, selecione **constante** como o **FlowType**e selecione **cloudFiltered** como o **destino Atributo**. Na **origem** , escreva **verdadeiro**. Clique em **adicionar** para guardar a regra.  
    ![3 de transformação de entrada](./media/how-to-connect-sync-configure-filtering/inbound3.png)  
12. Para concluir a configuração, tem de executar uma **Full sync**. Continue a ler a secção [aplicar e verificar as alterações](#apply-and-verify-changes).

Se for necessário, pode criar mais regras do primeiro tipo onde incluir mais objetos na sincronização.

### <a name="outbound-filtering"></a>Filtragem de saída
Em alguns casos, é necessário fazer a filtragem apenas depois de associar os objetos no metaverso. Por exemplo, poderá ser necessário examinar o atributo de correio da floresta de recursos e o atributo userPrincipalName da floresta de contas, para determinar se um objeto deve ser sincronizado. Nestes casos, vai criar a filtragem na regra de saída.

Neste exemplo, alterar a filtragem, para que apenas os utilizadores que têm os emails e userPrincipalName que termine em @contoso.com são sincronizados:

1. Inicie sessão no servidor que está em execução do Azure AD Connect, utilizando uma conta que seja membro do **ADSyncAdmins** grupo de segurança.
2. Iniciar **Editor de regras de sincronização** partir do **iniciar** menu.
3. Sob **tipo de regras**, clique em **saída**.
4. Dependendo da versão do Connect que utiliza, localizar a regra com o nome **expansão para AAD – utilizador associar** ou **horizontalmente para AAD - utilizador aderir SOAInAD**e clique em **editar**.
5. No pop-up, responder **Sim** para criar uma cópia da regra.
6. Sobre o **Descrição** página, alterar **precedência** para um valor não utilizado, como 50.
7. Clique em **filtro Scoping** na navegação do lado esquerdo e clique em **Adicionar cláusula**. Na **atributo**, selecione **correio**. Na **operador**, selecione **ENDSWITH**. Na **valor**, tipo  **\@contoso.com**e, em seguida, clique em **Adicionar cláusula**. Na **atributo**, selecione **userPrincipalName**. Na **operador**, selecione **ENDSWITH**. Na **valor**, tipo  **\@contoso.com**.
8. Clique em **Guardar**.
9. Para concluir a configuração, tem de executar uma **Full sync**. Continue a ler a secção [aplicar e verificar as alterações](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Aplicar e verificar as alterações
Após fazer as alterações de configuração, tem de as aplicar aos objetos que já estão presentes no sistema. Também pode ser que os objetos que não estão atualmente no motor de sincronização devem ser processados (e o motor de sincronização tem de ler o sistema de origem novamente para verificar o seu conteúdo).

Se tiver alterado a configuração, utilizando **domínio** ou **unidade organizacional** filtragem, em seguida, o que precisa fazer uma **importação completa**, seguido **Delta sincronização**.

Se tiver alterado a configuração, utilizando **atributo** filtragem, em seguida, o que precisa fazer uma **completa sincronização**.

Siga os passos abaixo:

1. Iniciar **serviço de sincronização** partir a **iniciar** menu.
2. Selecione **conectores**. Na **conectores** , selecione o conector em que tomou uma anteriormente a alteração de configuração. Na **ações**, selecione **executar**.  
   ![Conector executar](./media/how-to-connect-sync-configure-filtering/connectorrun.png)  
3. Na **perfis de execução**, selecione a operação que foi mencionada na secção anterior. Se precisa executar duas ações, execute o segundo depois de concluir primeiro. (A **estado** coluna é **Idle** para o conector selecionado.)

Após a sincronização, todas as alterações foram transitadas para ser exportada. Antes de realmente fazer as alterações no Azure AD, deve verificar que todas essas alterações estão corretas.

1. Inicie uma linha de comandos e aceda a `%Program Files%\Microsoft Azure AD Sync\bin`.
2. Execute `csexport "Name of Connector" %temp%\export.xml /f:x`.  
   O nome do conector está no serviço de sincronização. Ele tem um nome semelhante a "contoso.com – AAD" para o Azure AD.
3. Execute `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`.
4. Agora tem um ficheiro no % temp % com o nome export.csv que pode ser examinada no Microsoft Excel. Esse arquivo contém todas as alterações que estão prestes a ser exportado.
5. Efetue as alterações necessárias para a configuração ou de dados e execute estes passos novamente (importar, sincronizar e certifique-se) até que as alterações que estão prestes a ser exportado são os esperados.

Quando estiver satisfeito, exporte as alterações para o Azure AD.

1. Selecione **conectores**. Na **conectores** , selecione o conector do Azure AD. Na **ações**, selecione **executar**.
2. Na **perfis de execução**, selecione **exportar**.
3. Se as alterações de configuração eliminam muitos objetos, em seguida, verá um erro na exportação quando o número é superior ao limiar configurado (por predefinição 500). Se vir este erro, em seguida, terá de desativar temporariamente a "[impedir eliminações acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md)" funcionalidade.

Agora é hora de voltar a ativar o scheduler.

1. Iniciar **agendador de tarefas** partir do **iniciar** menu.
2. Diretamente sob **biblioteca do Programador de tarefas**, localize a tarefa com o nome **agendador do Azure AD Sync**, botão direito do mouse e selecione **ativar**.

## <a name="group-based-filtering"></a>Filtragem baseada em grupo
Pode configurar a baseada em grupo filtragem na primeira vez que instalar o Azure AD Connect utilizando [instalação personalizada](how-to-connect-install-custom.md#sync-filtering-based-on-groups). Destina-se para uma implementação piloto onde pretende que apenas um pequeno conjunto de objetos a serem sincronizados. Ao desativar a filtragem baseada em grupo, não pode ser ativado novamente. Ele possui *nepodporuje* para utilizar com base no grupo de filtragem numa configuração personalizada. Só é suportado para configurar esse recurso com o Assistente de instalação. Quando tiver concluído o piloto, em seguida, utilize uma das outras opções de filtragem neste tópico. Ao utilizar a filtragem baseada em UO em conjunto com a filtragem baseada em grupo, tem de ser incluída OU(s) onde se encontram o grupo e seus membros.

Durante a sincronização de várias florestas do AD, pode configurar a baseada em grupo filtragem ao especificar um grupo diferente para cada conector do AD. Se desejar para sincronizar um utilizador no AD de um floresta e o mesmo utilizador tem um ou mais correspondente objetos noutras florestas do AD, tem de garantir que o objeto de utilizador e todos os seus objetos correspondentes são dentro baseado no grupo de filtragem de âmbito. Para obter exemplos:

* Tem um utilizador numa floresta que tenha um objeto FSP (Principal de segurança externa) correspondente noutra floresta. Ambos os objetos devem ser dentro baseado no grupo de filtragem de âmbito. Caso contrário, o utilizador não será sincronizado para o Azure AD.

* Tem um utilizador numa floresta que tenha uma conta de recurso correspondente (por exemplo, caixa de correio ligada) noutra floresta. Além disso, ter configurado o Azure AD Connect para associar o utilizador com a conta de recurso. Ambos os objetos devem ser dentro baseado no grupo de filtragem de âmbito. Caso contrário, o utilizador não será sincronizado para o Azure AD.

* Tem um utilizador numa floresta que tenha um correio correspondente contacto noutra floresta. Além disso, ter configurado o Azure AD Connect para associar o utilizador com o contato de correio. Ambos os objetos devem ser dentro baseado no grupo de filtragem de âmbito. Caso contrário, o utilizador não será sincronizado para o Azure AD.


## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [do Azure AD Connect](how-to-connect-sync-whatis.md) configuração.
- Saiba mais sobre [integrar as identidades no local com o Azure AD](whatis-hybrid-identity.md).
