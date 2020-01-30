---
title: 'Sincronização Azure AD Connect: Filtragem de configuração / Microsoft Docs'
description: Explica como configurar a filtragem na sincronização Azure AD Connect.
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
ms.date: 03/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 983699dfbfe3e8fa332da4810d1514a11029077f
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768178"
---
# <a name="azure-ad-connect-sync-configure-filtering"></a>Sincronização do Azure AD Connect: configurar a filtragem
Ao utilizar a filtragem, pode controlar quais os objetos que aparecem no Azure Ative Directory (Azure AD) a partir do seu diretório no local. A configuração predefinida leva todos os objetos em todos os domínios das florestas configuradas. Em geral, esta é a configuração recomendada. Os utilizadores que usam as cargas de trabalho do Office 365, como o Exchange Online e o Skype para negócios, beneficiam de uma Lista de Endereços Globais completa para que possam enviar e-mails e ligar para todos. Com a configuração padrão, teriam a mesma experiência que teriam com uma implementação no local de Exchange ou Lync.

Em alguns casos, no entanto, é-lhe exigido que faça algumas alterações na configuração predefinida. Eis alguns exemplos:

* Planeia usar a topologia de [diretório multi-Azure.](plan-connect-topologies.md#each-object-only-once-in-an-azure-ad-tenant) Em seguida, é necessário aplicar um filtro para controlar quais os objetos sincronizados a um determinado diretório azure AD.
* Executa um piloto para o Azure ou office 365 e só quer um subconjunto de utilizadores em Azure AD. No pequeno piloto, não é importante ter uma Lista de Endereços Globais completa para demonstrar a funcionalidade.
* Tem muitas contas de serviço e outras contas não pessoais que não quer em Azure AD.
* Por razões de conformidade, não elimina nenhuma conta de utilizador no local. Só os desativa. Mas em Azure AD, só quer que as contas ativas estejam presentes.

Este artigo abrange como configurar os diferentes métodos de filtragem.

> [!IMPORTANT]
> A Microsoft não suporta a modificação ou operação de sincronizações do Azure AD Connect fora das ações anteriormente documentadas. Qualquer uma destas ações pode resultar num estado inconsistente ou não apoiado de sincronização azure AD Connect. Como resultado, a Microsoft não pode fornecer suporte técnico para tais implementações.

## <a name="basics-and-important-notes"></a>Básicos e notas importantes
Na sincronização Azure AD Connect, pode ativar a filtragem a qualquer momento. Se começar com uma configuração predefinida de sincronização de diretório e, em seguida, configurar a filtragem, os objetos que são filtrados já não estão sincronizados com a AD Azure. Devido a esta mudança, quaisquer objetos em AD Azure que tenham sido previamente sincronizados mas que foram depois filtrados são eliminados em Azure AD.

Antes de começar a fazer alterações na filtragem, certifique-se de que [desativa a tarefa programada para](#disable-the-scheduled-task) não exportar acidentalmente alterações que ainda não verificou estar correta.

Como a filtragem pode remover muitos objetos ao mesmo tempo, deve certificar-se de que os seus novos filtros estão corretos antes de começar a exportar quaisquer alterações para a AD Azure. Depois de completar os passos de configuração, recomendamos vivamente que siga os [passos de verificação](#apply-and-verify-changes) antes de exportar e faça alterações no Azure AD.

Para protegê-lo de eliminar muitos objetos por acidente, a função "[prevenir eliminações acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md)" está onda por defeito. Se eliminar muitos objetos devido à filtragem (500 por padrão), tem de seguir os passos deste artigo para permitir que os apagues passem para a AD Azure.

Se utilizar uma construção antes de novembro de 2015[(1.0.9125),](reference-connect-version-history.md#1091250)faça uma alteração na configuração do filtro e utilize a sincronização de hash de palavra-passe, então terá de desencadear uma sincronização completa de todas as palavras-passe depois de ter concluído a configuração. Para obter passos sobre como desencadear uma sincronização completa da palavra-passe, consulte [O Gatilho uma sincronização completa de todas as palavras-passe](tshoot-connect-password-hash-synchronization.md#trigger-a-full-sync-of-all-passwords). Se estiver na construção 1.0.9125 ou mais tarde, então a ação regular de **sincronização completa** também calcula se as palavras-passe devem ser sincronizadas e se este passo extra já não é necessário.

Se os objetos do **utilizador** terem sido apagados inadvertidamente em AD Azure devido a um erro de filtragem, pode recriar os objetos de utilizador em AD Azure removendo as suas configurações de filtragem. Depois podes sincronizar os teus diretórios outra vez. Esta ação restaura os utilizadores do caixote do lixo de reciclagem em Azure AD. No entanto, não é possível desapagar outros tipos de objetos. Por exemplo, se apagar acidentalmente um grupo de segurança e foi usado para acl um recurso, o grupo e os seus ACLs não podem ser recuperados.

O Azure AD Connect apenas elimina objetos que já considerou estarem no âmbito. Se houver objetos em AD Azure que foram criados por outro motor de sincronização e estes objetos não estiverem no âmbito, adicionar filtragem não os remove. Por exemplo, se começar com um servidor DirSync que criou uma cópia completa de todo o seu diretório em AD Azure, e instalar um novo servidor de sincronização Azure AD Connect em paralelo com a filtragem ativada desde o início, o Azure AD Connect não remove os objetos extras que são criados pelo DirSync.

A configuração de filtragem é retida quando instala ou atualiza para uma versão mais recente do Azure AD Connect. É sempre uma boa prática verificar que a configuração não foi inadvertidamente alterada após uma atualização para uma versão mais recente antes de executar o primeiro ciclo de sincronização.

Se tiver mais de uma floresta, então deve aplicar as configurações de filtragem que são descritas neste tópico a todas as florestas (assumindo que quer a mesma configuração para todas elas).

### <a name="disable-the-scheduled-task"></a>Desativar a tarefa programada
Para desativar o programador incorporado que desencadeia um ciclo de sincronização a cada 30 minutos, siga estes passos:

1. Vá a um pedido da PowerShell.
2. Executar `Set-ADSyncScheduler -SyncCycleEnabled $False` para desativar o programador.
3. Faça as alterações documentadas neste artigo.
4. Executar `Set-ADSyncScheduler -SyncCycleEnabled $True` para ativar o programador novamente.

**Se utilizar uma estrutura Azure AD Connect antes de 1.1.105.0**  
Para desativar a tarefa programada que desencadeia um ciclo de sincronização a cada três horas, siga estes passos:

1. Iniciar **o Programador** de Tarefas a partir do menu **Iniciar.**
2. Diretamente sob a Biblioteca do **Agendador**de Tarefas, encontre a tarefa chamada Programador de **Sincronização AD Azure,** clique à direita e selecione **Disable**.  
   ![](./media/how-to-connect-sync-configure-filtering/taskscheduler.png) de programador de tarefas  
3. Agora pode fazer alterações de configuração e executar manualmente o motor de sincronização a partir da consola Do Gestor de Serviços de **Sincronização.**

Depois de ter completado todas as suas alterações de filtragem, não se esqueça de voltar e **ativar** a tarefa novamente.

## <a name="filtering-options"></a>Opções de filtragem
Pode aplicar os seguintes tipos de configuração de filtragem na ferramenta de sincronização do diretório:

* Baseado no [**grupo:** ](#group-based-filtering)A filtragem com base num único grupo só pode ser configurada na instalação inicial utilizando o assistente de instalação.
* [**Baseado em domínios**](#domain-based-filtering): Ao utilizar esta opção, pode selecionar quais os domínios que sincronizam com o Azure AD. Também pode adicionar e remover domínios da configuração do motor sincronizado quando fizer alterações na sua infraestrutura no local depois de instalar o sincronizado Azure AD Connect.
* [**Unidade organizacional (OU)-baseada**](#organizational-unitbased-filtering): Ao utilizar esta opção, pode selecionar quais Os Eua sincronizam para Azure AD. Esta opção é para todos os tipos de objetos em OUs selecionados.
* [**Baseado no atributo**](#attribute-based-filtering): Ao utilizar esta opção, pode filtrar objetos com base nos valores dos atributos nos objetos. Também pode ter filtros diferentes para diferentes tipos de objetos.

Pode utilizar várias opções de filtragem ao mesmo tempo. Por exemplo, pode utilizar filtragem baseada em OU para incluir apenas objetos num OU. Ao mesmo tempo, pode utilizar a filtragem baseada em atributos para filtrar ainda mais os objetos. Quando utiliza vários métodos de filtragem, os filtros utilizam um "AND" lógico entre os filtros.

## <a name="domain-based-filtering"></a>Filtragem baseada em domínios
Esta secção fornece-lhe os passos para configurar o filtro de domínio. Se adicionou ou removeu domínios na sua floresta depois de ter instalado o Azure AD Connect, também tem de atualizar a configuração de filtragem.

A forma preferida de alterar a filtragem baseada em domínios é executando o assistente de instalação e alterando o [domínio e filtrando a U](how-to-connect-install-custom.md#domain-and-ou-filtering). O assistente de instalação automatiza todas as tarefas documentadas neste tópico.

Só deve seguir estes passos se não conseguir executar o assistente de instalação por alguma razão.

A configuração de filtragem baseada em domínios consiste nestes passos:

1. Selecione os domínios que pretende incluir na sincronização.
2. Para cada domínio adicionado e removido, ajuste os perfis de execução.
3. [Aplicar e verificar alterações](#apply-and-verify-changes).

### <a name="select-the-domains-to-be-synchronized"></a>Selecione os domínios a sincronizar
Existem duas formas de selecionar os domínios a sincronizar:
    - Utilização do Serviço de Sincronização
    - Utilizando o assistente Azure AD Connect.


#### <a name="select-the-domains-to-be-synchronized-using-the-synchronization-service"></a>Selecione os domínios a sincronizar utilizando o Serviço de Sincronização
Para definir o filtro de domínio, faça os seguintes passos:

1. Inscreva-se no servidor que está a executar a sincronização Do Azure AD Connect utilizando uma conta que é membro do grupo de segurança **ADSyncAdmins.**
2. Inicie o Serviço de **Sincronização** a partir do menu **Iniciar.**
3. Selecione **Conectores,** e na lista de **Conectores,** selecione o Conector com o tipo **Deserviço Ativo**. Em **Ações,** selecione **Propriedades**.  
   propriedades do Conector ![](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Clique em **Configurar Partições**de Diretório .
5. Na lista de partições de **diretório Select,** selecione e desmarque os domínios conforme necessário. Verifique se apenas são selecionadas as divisórias que pretende sincronizar.  
   ![Partições](./media/how-to-connect-sync-configure-filtering/connectorpartitions.png)  
   Se alterou a sua infraestrutura de Diretório Ativo no local e adicionou ou removeu domínios da floresta, clique no botão **Refresh** para obter uma lista atualizada. Quando te refrescares, pedem-te credenciais. Forneça quaisquer credenciais com acesso a leitura ao Diretório Ativo do Windows Server. Não tem de ser o utilizador que está pré-povoado na caixa de diálogo.  
   ![Refresh necessário](./media/how-to-connect-sync-configure-filtering/refreshneeded.png)  
6. Quando terminar, feche o diálogo **Properties** clicando **ok**. Se removeu os domínios da floresta, uma mensagem pop-up diz que um domínio foi removido e que a configuração será limpa.
7. Continue a ajustar os perfis de execução.

#### <a name="select-the-domains-to-be-synchronized-using-the-azure-ad-connect-wizard"></a>Selecione os domínios a sincronizar utilizando o assistente Azure AD Connect
Para definir o filtro de domínio, faça os seguintes passos:

1.  Inicie o assistente Azure AD Connect
2.  Clique em **Configurar**.
3.  **Selecione Personalizar opções** de sincronização e clicar **em Seguinte**.
4.  Introduzir as credenciais do Azure AD
5.  No ecrã dos **Diretórios Conectados** clique em **Seguinte**.
6.  Na página de **filtragem de Domínio e OU** clique em **Refresh**.  Novos domínios doentes aparecem agora e os domínios apagados desaparecerão.
   ![Partições](./media/how-to-connect-sync-configure-filtering/update2.png)  

### <a name="update-the-run-profiles"></a>Atualizar os perfis de execução
Se atualizou o filtro de domínio, também precisa de atualizar os perfis de execução.

1. Na lista de **Conectores,** certifique-se de que o Conector que mudou no passo anterior é selecionado. Em **Ações,** **selecione Configure Perfis**de execução .  
   ![perfis de execução do Connector 1](./media/how-to-connect-sync-configure-filtering/connectorrunprofiles1.png)  
2. Encontre e identifique os seguintes perfis:
    * Importação integral
    * Sincronização completa
    * Importação Delta
    * Sincronização Delta
    * Exportar
3. Para cada perfil, ajuste os domínios **adicionados** e **removidos.**
    1. Para cada um dos cinco perfis, faça os seguintes passos para cada domínio **adicionado:**
        1. Selecione o perfil de execução e clique em **New Step**.
        2. Na página **Configure Step,** no menu de entrega do **tipo,** selecione o tipo de passo com o mesmo nome que o perfil que está a configurar. Clique depois em **Seguinte**.  
        perfis de execução do Conector ![2](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep1.png)  
        3. Na página de Configuração do **Conector,** no menu de entrega da **partição,** selecione o nome do domínio que adicionou ao filtro de domínio.  
        perfis de execução do Conector ![3](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep2.png)  
        4. Para fechar o diálogo do Perfil de **Execução de Configurar,** clique em **Terminar**.
    2. Para cada um dos cinco perfis, faça os seguintes passos para cada domínio **removido:**
        1. Selecione o perfil de execução.
        2. Se o **valor** do atributo da **Partição** for um GUID, selecione o passo de execução e clique em **Eliminar Passo**.  
        perfis de execução do conector ![4](./media/how-to-connect-sync-configure-filtering/runprofilesdeletestep.png)  
    3. Verifique o seu troco. Cada domínio que pretende sincronizar deve ser listado como um passo em cada perfil de execução.
4. Para fechar o diálogo de perfis de **execução de configurar,** clique em **OK**.
5.  Para completar a configuração, é necessário executar uma **importação completa** e uma **sincronização Delta.** Continuar a ler a secção [Aplicar e verificar alterações](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Filtragem baseada em unidade organizacional
A forma preferida de alterar a filtragem baseada em OU é executando o assistente de instalação e alterando o [domínio e filtragem de OU](how-to-connect-install-custom.md#domain-and-ou-filtering). O assistente de instalação automatiza todas as tarefas documentadas neste tópico.

Só deve seguir estes passos se não conseguir executar o assistente de instalação por alguma razão.

Para configurar a filtragem baseada em unidade organizacional, faça os seguintes passos:

1. Inscreva-se no servidor que está a executar a sincronização Do Azure AD Connect utilizando uma conta que é membro do grupo de segurança **ADSyncAdmins.**
2. Inicie o Serviço de **Sincronização** a partir do menu **Iniciar.**
3. Selecione **Conectores,** e na lista de **Conectores,** selecione o Conector com o tipo **Deserviço Ativo**. Em **Ações,** selecione **Propriedades**.  
   propriedades do Conector ![](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Clique em **Configurar Partições de Diretório,** selecione o domínio que pretende configurar e, em seguida, clique em **Recipientes**.
5. Quando for solicitado, forneça quaisquer credenciais com acesso à leitura do seu Diretório Ativo no local. Não tem de ser o utilizador que está pré-povoado na caixa de diálogo.
6. Na caixa de diálogo **Select Containers,** limpe as OUs que não pretende sincronizar com o diretório da nuvem e, em seguida, clique em **OK**.  
   ![OUs na caixa de diálogo Select Containers](./media/how-to-connect-sync-configure-filtering/ou.png)  
   * O recipiente **Computadores** deve ser selecionado para que os seus computadores Windows 10 sejam sincronizados com sucesso para a AD Azure. Se os seus computadores unidos ao domínio estiverem localizados noutras OUs, certifique-se de que estes são selecionados.
   * O contentor **ForeignSecurityPrincipals** deverá ser selecionado, se tiver várias florestas com confianças. Este contentor permite resolver a associação ao grupo de segurança de múltiplas florestas.
   * O **RegisteredDevices** OU deve ser selecionado se tiver ativado a funcionalidade de reprodução do dispositivo. Se utilizar outra funcionalidade de redação, como a reprodução em grupo, certifique-se de que estes locais são selecionados.
   * Selecione qualquer outro OU onde utilizadores, iNetOrgPersons, grupos, contactos e computadores estejam localizados. Na imagem, todas estas OUs estão localizadas na ManagedObjects OU.
   * Se utilizar a filtragem baseada em grupo, então a Ou onde o grupo está localizado deve ser incluída.
   * Note que pode configurar se as novas OUs que são adicionadas após os acabamentos de configuração de filtragem são sincronizadas ou não sincronizadas. Consulte a próxima secção para mais detalhes.
7. Quando terminar, feche o diálogo **Properties** clicando **ok**.
8. Para completar a configuração, é necessário executar uma **importação completa** e uma **sincronização Delta.** Continuar a ler a secção [Aplicar e verificar alterações](#apply-and-verify-changes).

### <a name="synchronize-new-ous"></a>Sincronizar novas OUs
As novas OUs que são criadas após a configuração da filtragem são sincronizadas por padrão. Este estado é indicado por uma caixa de verificação selecionada. Também pode desseleccionar algumas sub-OUs. Para obter este comportamento, clique na caixa até ficar branca com uma marca de verificação azul (o seu estado padrão). Em seguida, desmarque qualquer sub-OUs que não queira sincronizar.

Se todas as Sub-OUs estiverem sincronizadas, então a caixa é branca com uma marca de verificação azul.  
![OU com todas as caixas selecionadas](./media/how-to-connect-sync-configure-filtering/ousyncnewall.png)

Se algumas sub-OUs não foram selecionadas, então a caixa é cinzenta com uma marca de verificação branca.  
![OU com algumas Sub-OUs não selecionadas](./media/how-to-connect-sync-configure-filtering/ousyncnew.png)

Com esta configuração, um novo OU que foi criado sob ManagedObjects é sincronizado.

O assistente de instalação Azure AD Connect cria sempre esta configuração.

### <a name="dont-synchronize-new-ous"></a>Não sincronize novas OUs
Pode configurar o motor de sincronização para não sincronizar novas OUs depois de a configuração de filtragem ter terminado. Este estado é indicado na UI pela caixa que aparece em cinza sólido sem marca de verificação. Para obter este comportamento, clique na caixa até ficar branca sem marca de verificação. Em seguida, selecione as sub-OUs que pretende sincronizar.

![OU com a raiz não selecionada](./media/how-to-connect-sync-configure-filtering/oudonotsyncnew.png)

Com esta configuração, um novo OU que foi criado sob ManagedObjects não é sincronizado.

## <a name="attribute-based-filtering"></a>Filtragem baseada em atributos
Certifique-se de que está a usar o novembro de 2015[(1.0.9125](reference-connect-version-history.md#1091250)) ou posteriormente construir para que estes passos funcionem.

> [!IMPORTANT]
>A Microsoft recomenda não modificar as regras predefinidas criadas pelo **Azure AD Connect**. Se quiser modificar a regra, clone-a e desative a regra original. Faça qualquer alteração à regra clonada. Por favor, note que ao fazê-lo (regra original incapacitante) perderá quaisquer correções ou funcionalidades de bugs ativadas através dessa regra.

A filtragem baseada em atributos é a forma mais flexível de filtrar objetos. Você pode usar o poder de [disposição declarativa](concept-azure-ad-connect-sync-declarative-provisioning.md) para controlar quase todos os aspetos de quando um objeto é sincronizado com AD Azure.

Pode aplicar a filtragem de [entrada](#inbound-filtering) do Diretório Ativo ao metaverso e a filtragem de [saída](#outbound-filtering) do metaverso para o AD Azure. Recomendamos que aplique a filtragem de entrada porque é a mais fácil de manter. Só deve utilizar a filtragem de saída se for necessário juntar objetos de mais de uma floresta antes de a avaliação poder ocorrer.

### <a name="inbound-filtering"></a>Filtragem de entrada
A filtragem de entrada utiliza a configuração predefinida, onde os objetos que vão para a AD Azure devem ter a nuvem de atributo metaverso Filtrada não definida para um valor a ser sincronizado. Se o valor deste atributo for definido para **True,** então o objeto não é sincronizado. Não deve ser definido para **Falso,** por design. Para garantir que outras regras têm a capacidade de contribuir com um valor, este atributo só deve ter os valores **Verdadeiro** ou **NULO** (ausente).

Na filtragem de entrada, utiliza-se o poder de **alcance** para determinar quais os objetos a sincronizar ou não a sincronizar. É aqui que faz ajustes para se adaptar aos requisitos da sua própria organização. O módulo de âmbito tem um **grupo** e uma **cláusula** para determinar quando uma regra de sincronização está no âmbito. Um grupo contém uma ou muitas cláusulas. Há um "AND" lógico entre várias cláusulas, e um "OR" lógico entre vários grupos.

Vejamos um exemplo:  
![Âmbito](./media/how-to-connect-sync-configure-filtering/scope.png)  
Isto deve ser lido como **(departamento = TI) OR (departamento = Vendas E c = EUA)** .

Nas seguintes amostras e passos, utiliza o objeto do utilizador como exemplo, mas pode usá-lo para todos os tipos de objetos.

Nas seguintes amostras, o valor de precedência começa com 50. Isto pode ser qualquer número não utilizado, mas deve ser inferior a 100.

#### <a name="negative-filtering-do-not-sync-these"></a>Filtragem negativa: "não sincronize estes"
No exemplo seguinte, filtra -se (não sincronizar) todos os utilizadores onde a **extensãoAttribute15** tem o valor **NoSync**.

1. Inscreva-se no servidor que está a executar a sincronização Do Azure AD Connect utilizando uma conta que é membro do grupo de segurança **ADSyncAdmins.**
2. Inicie regras de **sincronização Editor** a partir do menu **Iniciar.**
3. **Certifique-se de que a Entrada** está selecionada e clique em Adicionar Nova **Regra**.
4. Dê à regra um nome descritivo, como "*In from AD – User DoNotSyncFilter*". Selecione a floresta correta, selecione **User** como o tipo de **objeto CS**, e selecione **Pessoa** como o tipo de **objeto MV**. No **Tipo de Link,** selecione **Juntar**. Em **Precedência**, escreva um valor que não é usado atualmente por outra regra de sincronização (por exemplo 50), e depois clique em **Next**.  
   ![](./media/how-to-connect-sync-configure-filtering/inbound1.png) de descrição de entrada 1  
5. No **filtro De scoping,** clique em **Adicionar Grupo**, e clique em Adicionar **Cláusula**. No **Atributo,** **selecione ExtensionAttribute15**. Certifique-se de que **o Operador** está definido para **EQUAL**, e digite o valor **NoSync** na caixa **Valor.** Clique em **Seguinte**.  
   ![](./media/how-to-connect-sync-configure-filtering/inbound2.png) de âmbito de entrada 2  
6. Deixe as regras **de Unir** vazias e, em seguida, clique **em Seguinte**.
7. Clique em **Adicionar Transformação,** selecione o **FlowType** como **Constante**, e selecione **cloudFiltered** como o **Atributo do Alvo**. Na caixa de texto **Fonte,** escreva **True**. Clique em **Adicionar** para salvar a regra.  
   ![](./media/how-to-connect-sync-configure-filtering/inbound3.png) de transformação inbound 3
8. Para completar a configuração, é necessário executar uma **sincronização completa**. Continuar a ler a secção [Aplicar e verificar alterações](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Filtragem positiva: "apenas sincronizar estes"
Expressar filtragem positiva pode ser mais desafiante porque também tem de considerar objetos que não são óbvios para serem sincronizados, como salas de conferências. Também vai substituir o filtro predefinido na regra fora da caixa **Dentro de AD - User Join**. Quando criar o filtro personalizado, certifique-se de não incluir objetos críticos do sistema, objetos de conflito de replicação, caixas de correio especiais e as contas de serviço do Azure AD Connect.

A opção de filtragem positiva requer duas regras de sincronização. É necessário uma regra (ou várias) com o âmbito correto dos objetos para sincronizar. Você também precisa de uma segunda regra de sincronização catch-all que filtra todos os objetos que ainda não foram identificados como um objeto que deve ser sincronizado.

No exemplo seguinte, apenas sincroniza os objetos de utilizador onde o atributo do departamento tem o valor **Vendas**.

1. Inscreva-se no servidor que está a executar a sincronização Do Azure AD Connect utilizando uma conta que é membro do grupo de segurança **ADSyncAdmins.**
2. Inicie regras de **sincronização Editor** a partir do menu **Iniciar.**
3. **Certifique-se de que a Entrada** está selecionada e clique em Adicionar Nova **Regra**.
4. Dê à regra um nome descritivo, como "*In from AD – User Sales sync*". Selecione a floresta correta, selecione **User** como o tipo de **objeto CS**, e selecione **Pessoa** como o tipo de **objeto MV**. No **Tipo de Link,** selecione **Juntar**. Em **Precedência**, escreva um valor que não é usado atualmente por outra regra de sincronização (por exemplo 51), e depois clique em **Next**.  
   ![](./media/how-to-connect-sync-configure-filtering/inbound4.png) de descrição de entrada 4  
5. No **filtro De scoping,** clique em **Adicionar Grupo**, e clique em Adicionar **Cláusula**. No **Attribute,** selecione **departamento**. Certifique-se de que o Operador está definido para **EQUAL**, e digite o valor **Vendas** na caixa **valor.** Clique em **Seguinte**.  
   ![](./media/how-to-connect-sync-configure-filtering/inbound5.png) de âmbito de entrada 5  
6. Deixe as regras **de Unir** vazias e, em seguida, clique **em Seguinte**.
7. Clique em **Adicionar Transformação,** selecione **Constant** como o **FlowType**, e selecione a **nuvem Filtrada** como o **Atributo do Alvo**. Na caixa **Fonte,** escreva **Falso**. Clique em **Adicionar** para salvar a regra.  
   ![](./media/how-to-connect-sync-configure-filtering/inbound6.png) de transformação Inbound 6  
   Este é um caso especial onde configura explicitamente a nuvemFiltrada para **Falsa**.
8. Agora temos de criar a regra da sincronização catch-all. Dê à regra um nome descritivo, como "*In from AD – User Catch-all filter*". Selecione a floresta correta, selecione **User** como o tipo de **objeto CS**, e selecione **Pessoa** como o tipo de **objeto MV**. No **Tipo de Link,** selecione **Juntar**. Em **Precedência**, escreva um valor que não é atualmente usado por outra Regra de Sincronização (por exemplo 99). Selecionou um valor de precedência superior (precedência inferior) ao anterior. Mas também deixou espaço para que possa adicionar mais regras de sincronização de filtragem mais tarde quando quiser começar a sincronizar departamentos adicionais. Clique em **Seguinte**.  
   ![](./media/how-to-connect-sync-configure-filtering/inbound7.png) de descrição de entrada 7  
9. Deixe **o filtro de deteção** vazio e clique em **Seguinte**. Um filtro vazio indica que a regra deve ser aplicada a todos os objetos.
10. Deixe as regras **de Unir** vazias e, em seguida, clique **em Seguinte**.
11. Clique em **Adicionar Transformação,** selecione **Constant** como o **FlowType**, e selecione **cloudFiltered** como o **Atributo do Alvo**. Na caixa **Fonte,** escreva **True**. Clique em **Adicionar** para salvar a regra.  
    ![](./media/how-to-connect-sync-configure-filtering/inbound3.png) de transformação inbound 3  
12. Para completar a configuração, é necessário executar uma **sincronização completa**. Continuar a ler a secção [Aplicar e verificar alterações](#apply-and-verify-changes).

Se precisar, pode criar mais regras do primeiro tipo onde inclui mais objetos na sincronização.

### <a name="outbound-filtering"></a>Filtragem de saída
Em alguns casos, é necessário fazer a filtragem apenas depois de os objetos se terem juntado ao metaverso. Por exemplo, pode ser necessário olhar para o atributo de correio da floresta de recursos, e o atributo userPrincipalName da floresta de conta, para determinar se um objeto deve ser sincronizado. Nestes casos, cria-se a filtragem na regra de saída.

Neste exemplo, altera a filtragem de modo a que apenas os utilizadores que tenham o seu mail e userPrincipalName que terminam em @contoso.com sejam sincronizados:

1. Inscreva-se no servidor que está a executar a sincronização Do Azure AD Connect utilizando uma conta que é membro do grupo de segurança **ADSyncAdmins.**
2. Inicie regras de **sincronização Editor** a partir do menu **Iniciar.**
3. De acordo com o Tipo de **Regras,** clique em **Outbound**.
4. Dependendo da versão do Connect que utiliza, encontre a regra denominada **out to AAD – User Join** ou out to **AAD - User Join SOAInAD**, e clique em **Editar**.
5. No pop-up, responda **Sim** para criar uma cópia da regra.
6. Na página **Descrição,** mude a **Precedência** para um valor não utilizado, como 50.
7. Clique no **filtro De scoping** na navegação à esquerda e, em seguida, clique em **Adicionar cláusula**. No **Atributo,** selecione **correio**. No **Operador,** selecione **ENDSWITH**. Em **Valor**, **escreva\@contoso.com,** e, em seguida, clique em **Adicionar cláusula**. No **Atributo, selecione** **userPrincipalName**. No **Operador,** selecione **ENDSWITH**. Em **Valor,** digite **contoso.com\@.**
8. Clique em **Guardar**.
9. Para completar a configuração, é necessário executar uma **sincronização completa**. Continuar a ler a secção [Aplicar e verificar alterações](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Aplicar e verificar alterações
Depois de ter feito alterações de configuração, deve aplicá-las aos objetos que já estão presentes no sistema. Pode também ser que os objetos que não estão atualmente no motor de sincronização devem ser processados (e o motor sincronizado precisa de voltar a ler o sistema de origem para verificar o seu conteúdo).

Se alterou a configuração utilizando a filtragem de **domínio** ou **unidade organizacional,** então precisa de fazer uma **importação completa**, seguida de **sincronização Delta**.

Se alterou a configuração utilizando a filtragem do **atributo,** então tem de fazer uma **sincronização completa**.

Execute as seguintes etapas:

1. Inicie o Serviço de **Sincronização** a partir do menu **Iniciar.**
2. Selecione **Conectores**. Na lista de **Conectores,** selecione o Conector onde fez uma alteração de configuração mais cedo. Em **Ações,** selecione **Executar**.  
   ![connector executar](./media/how-to-connect-sync-configure-filtering/connectorrun.png)  
3. Nos **perfis de Execução,** selecione a operação que foi mencionada na secção anterior. Se precisar escorrer duas ações, corra a segunda depois que a primeira termine. (A coluna **do Estado** é **idle** para o conector selecionado.)

Após a sincronização, todas as alterações são encenadas para serem exportadas. Antes de efazer as alterações em Azure AD, pretende verificar se todas estas alterações estão corretas.

1. Inicie uma solicitação de comando e vá para `%ProgramFiles%\Microsoft Azure AD Sync\bin`.
2. Execute `csexport "Name of Connector" %temp%\export.xml /f:x`.  
   O nome do Conector está no Serviço de Sincronização. Tem um nome semelhante a "contoso.com – AAD" para a Azure AD.
3. Execute `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`.
4. Tem agora um ficheiro em %temporário chamado export.csv que pode ser examinado no Microsoft Excel. Este ficheiro contém todas as alterações que estão prestes a ser exportadas.
5. Faça as alterações necessárias aos dados ou configurações, e faça estes passos novamente (Importar, Sincronizar e Verificar) até que as alterações que estão prestes a ser exportadas sejam o que espera.

Quando estiver satisfeito, exporte as alterações para a Azure AD.

1. Selecione **Conectores**. Na lista de **Conectores,** selecione o Conector AD Azure. Em **Ações,** selecione **Executar**.
2. Nos **perfis de execução,** selecione **Export .**
3. Se as alterações de configuração eliminarem muitos objetos, então verá um erro na exportação quando o número é superior ao limiar configurado (por predefinição de 500). Se vir este erro, então terá de desativar temporariamente a função "[prevenir apagar acidentalmente](how-to-connect-sync-feature-prevent-accidental-deletes.md)".

Agora é hora de ativar o agendador novamente.

1. Iniciar **o Programador** de Tarefas a partir do menu **Iniciar.**
2. Diretamente sob a Biblioteca do **Agendador**de Tarefas, encontre a tarefa chamada Programador de **Sincronização AD Azure,** clique à direita e selecione **Enable**.

## <a name="group-based-filtering"></a>Filtragem baseada em grupo
Pode configurar a filtragem baseada em grupo na primeira vez que instala o Azure AD Connect utilizando [uma instalação personalizada](how-to-connect-install-custom.md#sync-filtering-based-on-groups). Destina-se a uma implantação piloto onde se deseja apenas um pequeno conjunto de objetos para ser sincronizado. Quando desativa a filtragem baseada em grupo, não pode ser ativada novamente. Não é *suportado* para usar filtragem baseada em grupo numa configuração personalizada. É suportado apenas para configurar esta funcionalidade utilizando o assistente de instalação. Quando tiver terminado o seu piloto, use uma das outras opções de filtragem neste tópico. Ao utilizar a filtragem baseada em OU em conjunto com a filtragem baseada em grupo, os OU(s) onde o grupo e os seus membros estão localizados devem ser incluídos.

Ao sincronizar várias florestas ad,pode configurar a filtragem baseada em grupo especificando um grupo diferente para cada conector aD. Se pretender sincronizar um utilizador numa floresta ad.C. e o mesmo utilizador tiver um ou mais objetos correspondentes noutras florestas adc, deve certificar-se de que o objeto do utilizador e todos os seus objetos correspondentes estão dentro do âmbito de filtragem baseado em grupo. Para obter exemplos:

* Você tem um utilizador em uma floresta que tem um objeto FSP correspondente (Diretor de Segurança Estrangeira) em outra floresta. Ambos os objetos devem estar dentro do âmbito de filtragem baseado em grupo. Caso contrário, o utilizador não será sincronizado com a AD Azure.

* Tem um utilizador numa floresta que tem uma conta de recursos correspondente (por exemplo, caixa de correio ligada) noutra floresta. Além disso, configurou o Azure AD Connect para ligar o utilizador à conta de recursos. Ambos os objetos devem estar dentro do âmbito de filtragem baseado em grupo. Caso contrário, o utilizador não será sincronizado com a AD Azure.

* Você tem um utilizador em uma floresta que tem um contacto de correio correspondente em outra floresta. Além disso, configurou o Azure AD Connect para ligar o utilizador ao contacto com o correio. Ambos os objetos devem estar dentro do âmbito de filtragem baseado em grupo. Caso contrário, o utilizador não será sincronizado com a AD Azure.


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre a configuração de [sincronização do Azure AD Connect.](how-to-connect-sync-whatis.md)
- Saiba mais sobre [a integração das suas identidades no local com a Azure AD](whatis-hybrid-identity.md).
