---
title: 'Azure AD Connect sync: Configure filtering / Microsoft Docs'
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
ms.topic: how-to
ms.date: 03/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a68d7574d16485c378f6066a652471d52fa0c30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319984"
---
# <a name="azure-ad-connect-sync-configure-filtering"></a>Sincronização do Azure AD Connect: configurar a filtragem
Ao utilizar a filtragem, pode controlar quais os objetos que aparecem no Diretório Ativo Azure (AD) a partir do seu diretório no local. A configuração padrão leva todos os objetos em todos os domínios nas florestas configuradas. Em geral, esta é a configuração recomendada. Os utilizadores que utilizam cargas de trabalho microsoft 365, como Exchange Online e Skype for Business, beneficiam de uma lista completa de endereços globais para que possam enviar e-mails e ligar para todos. Com a configuração padrão, teriam a mesma experiência que teriam com uma implementação no local de Exchange ou Lync.

Em alguns casos, no entanto, é necessário fazer algumas alterações na configuração padrão. Eis alguns exemplos:

* Você planeia usar a [topologia de diretório ad multi-Azure.](plan-connect-topologies.md#each-object-only-once-in-an-azure-ad-tenant) Em seguida, é necessário aplicar um filtro para controlar quais objetos são sincronizados num determinado diretório AD Azure.
* Você dirige um piloto para Azure ou Microsoft 365 e você só quer um subconjunto de utilizadores em Azure AD. No pequeno piloto, não é importante ter uma Lista de Endereços Globais completa para demonstrar a funcionalidade.
* Tem muitas contas de serviço e outras contas não pessoais que não quer no Azure AD.
* Por razões de conformidade, não elimina nenhuma conta de utilizador no local. Só os desativa. Mas no Azure AD, só quer que as contas ativas estejam presentes.

Este artigo abrange como configurar os diferentes métodos de filtragem.

> [!IMPORTANT]
> A Microsoft não suporta a modificação ou operação de sincronizações do Azure AD Connect fora das ações anteriormente documentadas. Qualquer uma destas ações pode resultar num estado inconsistente ou não suportado da sincronização Azure AD Connect. Como resultado, a Microsoft não pode fornecer suporte técnico para tais implementações.

## <a name="basics-and-important-notes"></a>Básicos e notas importantes
Na sincronização Azure AD Connect, pode ativar a filtragem a qualquer momento. Se começar com uma configuração predefinida de sincronização de diretórios e, em seguida, configurar a filtragem, os objetos que são filtrados já não são sincronizados com Azure AD. Devido a esta alteração, quaisquer objetos em Azure AD que foram previamente sincronizados mas depois filtrados são eliminados em Azure AD.

Antes de começar a fazer alterações na filtragem, certifique-se de que [desativa a tarefa programada](#disable-the-scheduled-task) para não exportar acidentalmente alterações que ainda não verificou estarem corretas.

Uma vez que a filtragem pode remover muitos objetos ao mesmo tempo, deseja certificar-se de que os seus novos filtros estão corretos antes de começar a exportar quaisquer alterações para Azure AD. Depois de ter completado os passos de configuração, recomendamos vivamente que siga os [passos de verificação](#apply-and-verify-changes) antes de exportar e faça alterações no Azure AD.

Para protegê-lo de eliminar muitos objetos por acidente, a função "[evitar eliminações acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md)" está acesa por defeito. Se eliminar muitos objetos devido à filtragem (500 por defeito), tem de seguir os passos deste artigo para permitir que as eliminações passem para Azure AD.

Se utilizar uma construção antes de novembro de 2015 ([1.0.9125),](reference-connect-version-history.md)altere para uma configuração de filtro e utilize a sincronização de hash de palavra-passe, então tem de ativar uma sincronização completa de todas as palavras-passe depois de ter concluído a configuração. Para obter etapas sobre como desencadear uma sincronização completa da palavra-passe, consulte [Trigger uma sincronização completa de todas as palavras-passe](tshoot-connect-password-hash-synchronization.md#trigger-a-full-sync-of-all-passwords). Se estiver na construção 1.0.9125 ou mais tarde, então a ação regular **de sincronização completa** também calcula se as palavras-passe devem ser sincronizadas e se este passo extra já não é necessário.

Se os objetos **do utilizador** forem apagados inadvertidamente no AD AZure devido a um erro de filtragem, pode recriar os objetos do utilizador em AD Azure removendo as configurações de filtragem. Depois podes sincronizar os teus diretórios outra vez. Esta ação restaura os utilizadores do caixote do lixo em Azure AD. No entanto, não se pode desdelear outros tipos de objetos. Por exemplo, se eliminar acidentalmente um grupo de segurança e foi usado para a ACL um recurso, o grupo e os seus ACLs não podem ser recuperados.

O Azure AD Connect apenas elimina objetos que já considerou estarem no âmbito. Se houver objetos em Azure AD que foram criados por outro motor de sincronização e estes objetos não estão no âmbito, adicionar filtragem não os remove. Por exemplo, se começar com um servidor DirSync que criou uma cópia completa de todo o seu diretório em Azure AD, e instalar um novo servidor de sincronização Azure AD Connect em paralelo com a filtragem ativada desde o início, o Azure AD Connect não remove os objetos extra que são criados pela DirSync.

A configuração de filtragem é mantida quando instala ou atualiza para uma versão mais recente do Azure AD Connect. É sempre uma boa prática verificar se a configuração não foi alterada inadvertidamente após um upgrade para uma versão mais recente antes de executar o primeiro ciclo de sincronização.

Se tiver mais do que uma floresta, então deve aplicar as configurações de filtragem que são descritas neste tópico a todas as florestas (assumindo que deseja a mesma configuração para todas elas).

### <a name="disable-the-scheduled-task"></a>Desativar a tarefa programada
Para desativar o programador incorporado que desencadeia um ciclo de sincronização a cada 30 minutos, siga estes passos:

1. Vá a um pedido do PowerShell.
2. Corra `Set-ADSyncScheduler -SyncCycleEnabled $False` para desativar o programador.
3. Faça as alterações que estão documentadas neste artigo.
4. Corra `Set-ADSyncScheduler -SyncCycleEnabled $True` para ativar novamente o programador.

**Se utilizar uma construção AD AD antes de 1.1.105.0**  
Para desativar a tarefa programada que desencadeia um ciclo de sincronização de três em três horas, siga estes passos:

1. Iniciar **o Programador** de Tarefas a partir do menu **Iniciar.**
2. Diretamente na **Biblioteca do Programador de Tarefas,** encontre a tarefa chamada **Azure AD Sync Scheduler,** clique à direita e selecione **Disable**.  
   ![Programador de Tarefas](./media/how-to-connect-sync-configure-filtering/taskscheduler.png)  
3. Agora pode fazer alterações de configuração e executar o motor de sincronização manualmente a partir da consola **Do Gestor de Serviços de Sincronização.**

Depois de ter completado todas as alterações de filtragem, não se esqueça de voltar e de voltar a **ativar** a tarefa.

## <a name="filtering-options"></a>Opções de filtragem
Pode aplicar os seguintes tipos de configuração de filtragem na ferramenta de sincronização do diretório:

* [**Base de grupo**](#group-based-filtering): A filtragem baseada num único grupo só pode ser configurada na instalação inicial utilizando o assistente de instalação.
* [**Baseado em domínios**](#domain-based-filtering): Ao utilizar esta opção, pode selecionar quais os domínios sincronizados com Azure AD. Também pode adicionar e remover domínios da configuração do motor de sincronização quando escruisse as alterações na sua infraestrutura no local depois de instalar a sincronização Azure AD Connect.
* [**Unidade organizacional (OU)- baseada em**](#organizational-unitbased-filtering): Ao utilizar esta opção, pode selecionar quais as OUs sincronizam com Azure AD. Esta opção destina-se a todos os tipos de objetos em OUs selecionados.
* [**Baseado em atributos**](#attribute-based-filtering): Ao utilizar esta opção, pode filtrar objetos com base nos valores de atributos nos objetos. Também pode ter filtros diferentes para diferentes tipos de objetos.

Pode utilizar várias opções de filtragem ao mesmo tempo. Por exemplo, pode utilizar a filtragem baseada em U para incluir apenas objetos numa ou. Ao mesmo tempo, pode utilizar a filtragem baseada no atributo para filtrar ainda mais os objetos. Quando utiliza vários métodos de filtragem, os filtros utilizam um "E" lógico entre os filtros.

## <a name="domain-based-filtering"></a>Filtragem baseada em domínio
Esta secção fornece-lhe os passos para configurar o filtro de domínio. Se tiver adicionado ou removido domínios na sua floresta depois de ter instalado o Azure AD Connect, também terá de atualizar a configuração de filtragem.

A forma preferida de alterar a filtragem baseada em domínios é executando o assistente de instalação e alterando [o domínio e a filtragem da U](how-to-connect-install-custom.md#domain-and-ou-filtering). O assistente de instalação automatiza todas as tarefas que estão documentadas neste tópico.

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

1. Inscreva-se no servidor que está a executar a sincronização do Azure AD Connect utilizando uma conta que é membro do grupo de segurança **ADSyncAdmins.**
2. Iniciar **o Serviço de Sincronização** a partir do menu **Iniciar.**
3. Selecione **Conectores**, e na lista **de Conectores,** selecione o Conector com o tipo **serviços de domínio do diretório ativo**. Em **Ações,** selecione **Properties**.  
   ![Propriedades do conector](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Clique **em Configurar Partições do Diretório.**
5. Na lista **de divisórias de diretório Select,** selecione e desescole os domínios conforme necessário. Verifique se apenas as divisórias que pretende sincronizar estão selecionadas.  
   ![Screenshot que mostra as divisórias do diretório na janela "Propriedades".](./media/how-to-connect-sync-configure-filtering/connectorpartitions.png)  
   Se alterou a sua infraestrutura de Ative Directory no local e adicionou ou removeu domínios da floresta, clique no botão **Refresh** para obter uma lista atualizada. Quando te refrescas, pede-te credenciais. Fornecer quaisquer credenciais com acesso lido ao Windows Server Ative Directory. Não tem de ser o utilizador que está pré-voizado na caixa de diálogo.  
   ![Atualização necessária](./media/how-to-connect-sync-configure-filtering/refreshneeded.png)  
6. Quando terminar, feche o diálogo **Propriedades** clicando **OK**. Se remover os domínios da floresta, uma mensagem pop-up diz que um domínio foi removido e que a configuração será limpa.
7. Continue a ajustar os perfis de execução.

#### <a name="select-the-domains-to-be-synchronized-using-the-azure-ad-connect-wizard"></a>Selecione os domínios a sincronizar utilizando o assistente Azure AD Connect
Para definir o filtro de domínio, faça os seguintes passos:

1.  Inicie o assistente Azure AD Connect
2.  Clique em **Configurar**.
3.  **Selecione Personalizar Opções de Sincronização** e clique em **Seguinte**.
4.  Introduzir as credenciais do Azure AD
5.  No ecrã **de Diretórios Conectados** clique em **Seguinte**.
6.  Na página de **filtragem Do Domínio e da UM** clique em **Refresh**.  Novos domínios iii agora aparecem e domínios apagados desaparecerão.
   ![Partições](./media/how-to-connect-sync-configure-filtering/update2.png)  

### <a name="update-the-run-profiles"></a>Atualizar os perfis de execução
Se atualizou o filtro de domínio, também precisa de atualizar os perfis de execução.

1. Na lista **de Conectores,** certifique-se de que o Conector que alterou no passo anterior é selecionado. Em **Ações**, **selecione Configure Run Profiles**.  
   ![Perfis de execução do conector 1](./media/how-to-connect-sync-configure-filtering/connectorrunprofiles1.png)  
2. Encontrar e identificar os seguintes perfis:
    * Importação Completa
    * Sincronização Completa
    * Importação Delta
    * Sincronização Delta
    * Exportar
3. Para cada perfil, ajuste os domínios **adicionados** e **removidos.**
    1. Para cada um dos cinco perfis, faça os seguintes passos para cada domínio **adicionado:**
        1. Selecione o perfil de execução e clique em **Novo Passo**.
        2. Na página **Configure Step,** no menu **Type** drop-down, selecione o tipo de passo com o mesmo nome que o perfil que está a configurar. Em seguida, clique em **Seguinte**.  
        ![Perfis de execução do conector 2](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep1.png)  
        3. Na página de Configuração do **Conector,** no menu suspenso da **partição,** selecione o nome do domínio que adicionou ao filtro de domínio.  
        ![Perfis de execução do conector 3](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep2.png)  
        4. Para fechar o diálogo de perfil de **execução configure,** clique em **Terminar**.
    2. Para cada um dos cinco perfis, faça os seguintes passos para cada domínio **removido:**
        1. Selecione o perfil de execução.
        2. Se o **valor** do atributo **Partição** for um GUID, selecione o passo de execução e clique em **Eliminar Step**.  
        ![Perfis de execução do conector 4](./media/how-to-connect-sync-configure-filtering/runprofilesdeletestep.png)  
    3. Verifique o seu troco. Cada domínio que pretende sincronizar deve ser listado como um passo em cada perfil de execução.
4. Para fechar o diálogo **Configure Run Profiles,** clique em **OK**.
5.  Para completar a configuração, é necessário executar uma **importação completa** e uma **sincronização Delta.** Continuar a ler A secção [Aplicar e verificar alterações](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Filtragem baseada em unidades organizacionais
A forma preferida de alterar a filtragem baseada em U é executando o assistente de instalação e alterando o [domínio e a filtragem da U](how-to-connect-install-custom.md#domain-and-ou-filtering). O assistente de instalação automatiza todas as tarefas que estão documentadas neste tópico.

Só deve seguir estes passos se não conseguir executar o assistente de instalação por alguma razão.

Para configurar a filtragem organizacional baseada em unidades, faça os seguintes passos:

1. Inscreva-se no servidor que está a executar a sincronização do Azure AD Connect utilizando uma conta que é membro do grupo de segurança **ADSyncAdmins.**
2. Iniciar **o Serviço de Sincronização** a partir do menu **Iniciar.**
3. Selecione **Conectores**, e na lista **de Conectores,** selecione o Conector com o tipo **serviços de domínio do diretório ativo**. Em **Ações,** selecione **Properties**.  
   ![Propriedades do conector](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Clique **em Configurar Partições do Diretório,** selecione o domínio que pretende configurar e, em seguida, clique em **Recipientes**.
5. Quando for solicitado, forneça quaisquer credenciais com acesso lido ao seu Ative Directory no local. Não tem de ser o utilizador que está pré-voizado na caixa de diálogo.
6. Na caixa de diálogo **Select Containers,** limpe as OUs que não pretende sincronizar com o diretório de nuvens e, em seguida, clique **em OK**.  
   ![OUs na caixa de diálogo select containers](./media/how-to-connect-sync-configure-filtering/ou.png)  
   * O contentor **Computers** deve ser selecionado para que os seus computadores Windows 10 sejam sincronizados com sucesso no AZure AD. Se os seus computadores unidos ao domínio estiverem localizados noutras OUs, certifique-se de que estes são selecionados.
   * O contentor **ForeignSecurityPrincipals** deverá ser selecionado, se tiver várias florestas com confianças. Este contentor permite resolver a associação ao grupo de segurança de múltiplas florestas.
   * O **Registodevices** OU deve ser selecionado se ativar a função de writeback do dispositivo. Se utilizar outra funcionalidade de writeback, como a redução de grupo, certifique-se de que estas localizações estão selecionadas.
   * Selecione qualquer outro OU onde estejam localizados utilizadores, iNetOrgPersons, Grupos, Contactos e Computadores. Na imagem, todas estas OUs estão localizadas no ManagedObjects OU.
   * Se utilizar filtragem baseada em grupo, o U onde o grupo está localizado deve ser incluído.
   * Note que pode configurar se as novas OUs adicionadas após os acabamentos de configuração de filtragem são sincronizadas ou não sincronizadas. Veja a secção seguinte para obter mais informações.
7. Quando terminar, feche o diálogo **Propriedades** clicando **OK**.
8. Para completar a configuração, é necessário executar uma **importação completa** e uma **sincronização Delta.** Continuar a ler A secção [Aplicar e verificar alterações](#apply-and-verify-changes).

### <a name="synchronize-new-ous"></a>Sincronizar novas OUs
As novas OUs criadas após a filtragem são sincronizadas por padrão. Este estado é indicado por uma caixa de verificação selecionada. Também pode desmarcar alguns sub-OUs. Para obter este comportamento, clique na caixa até ficar branca com uma marca de verificação azul (o seu estado padrão). Em seguida, desescolh quaisquer sub-OUs que não queira sincronizar.

Se todos os sub-OUs estiverem sincronizados, então a caixa é branca com uma marca de verificação azul.  
![OU com todas as caixas selecionadas](./media/how-to-connect-sync-configure-filtering/ousyncnewall.png)

Se alguns sub-OUs não foram selecionados, então a caixa é cinzenta com uma marca de verificação branca.  
![OU com alguns sub-OUs não selecionados](./media/how-to-connect-sync-configure-filtering/ousyncnew.png)

Com esta configuração, um novo OU que foi criado sob ManagedObjects é sincronizado.

O assistente de instalação Azure AD Connect cria sempre esta configuração.

### <a name="dont-synchronize-new-ous"></a>Não sincronize novas OUs
Pode configurar o motor de sincronização para não sincronizar novos OUs depois de terminada a configuração de filtragem. Este estado é indicado na UI pela caixa que aparece cinza sólido sem marca de verificação. Para obter este comportamento, clique na caixa até ficar branca sem marca de verificação. Em seguida, selecione as sub-OUs que pretende sincronizar.

![OU com a raiz não eleita](./media/how-to-connect-sync-configure-filtering/oudonotsyncnew.png)

Com esta configuração, um novo OU que foi criado sob ManagedObjects não é sincronizado.

## <a name="attribute-based-filtering"></a>Filtragem baseada em atributos
Certifique-se de que está a usar o novembro de[2015 (1.0.9125)](reference-connect-version-history.md)ou posteriormente construir para que estes passos funcionem.

> [!IMPORTANT]
>A Microsoft recomenda não modificar as regras padrão criadas pelo **Azure AD Connect**. Se quiser modificar a regra, clone-a e desative a regra original. Faça qualquer alteração à regra clonada. Tenha em atenção que ao fazê-lo (desativando a regra original) perderá quaisquer correções ou funcionalidades ativadas através dessa regra.

A filtragem baseada em atributos é a forma mais flexível de filtrar objetos. Você pode usar o poder de [provisão declarativa](concept-azure-ad-connect-sync-declarative-provisioning.md) para controlar quase todos os aspetos de quando um objeto é sincronizado com Azure AD.

Pode aplicar a filtragem de [entrada](#inbound-filtering) do Ative Directory ao metaverso e a filtragem de [saída](#outbound-filtering) do metaverso para a AD AZure. Recomendamos que aplique filtragem de entrada porque é a mais fácil de manter. Só deve utilizar a filtragem de saída se for necessário juntar objetos de mais de uma floresta antes de a avaliação poder ocorrer.

### <a name="inbound-filtering"></a>Filtragem de entrada
A filtragem de entrada utiliza a configuração padrão, onde os objetos que vão para Azure AD devem ter o atributo metaverso cloudFiltered não definido para um valor a ser sincronizado. Se o valor deste atributo for definido como **True,** então o objeto não é sincronizado. Não deve ser definido como **Falso,** por design. Para garantir que outras regras têm a capacidade de contribuir com um valor, este atributo só deve ter os valores **Verdadeiro** ou **NULO** (ausente).

Na filtragem de entrada, utiliza-se o poder de **mira** para determinar quais os objetos que sincronizam ou não sincronizam. É aqui que faz ajustes que se adequam às exigências da sua própria organização. O módulo de âmbito tem um **grupo** e uma **cláusula** para determinar quando uma regra de sincronização está no âmbito. Um grupo contém uma ou muitas cláusulas. Há um "E" lógico entre várias cláusulas, e um "OR" lógico entre vários grupos.

Vejamos um exemplo:  
![Uma imagem mostrando um exemplo de adicionar filtros de deteção.](./media/how-to-connect-sync-configure-filtering/scope.png)  
Isto deve ser lido como **(departamento = TI) OU (departamento = Vendas E c = EUA)**.

Nas seguintes amostras e passos, utiliza o objeto do utilizador como exemplo, mas pode usá-lo para todos os tipos de objetos.

Nas seguintes amostras, o valor de precedência começa com 50. Este pode ser qualquer número não utilizado, mas deve ser inferior a 100.

#### <a name="negative-filtering-do-not-sync-these"></a>Filtragem negativa: "não sincronize estes"
No exemplo seguinte, filtra (não sincronizar) todos os utilizadores onde **a extensãoAttribute15** tem o valor **NoSync**.

1. Inscreva-se no servidor que está a executar a sincronização do Azure AD Connect utilizando uma conta que é membro do grupo de segurança **ADSyncAdmins.**
2. Iniciar **o Editor de Regras** de Sincronização a partir do menu **Iniciar.**
3. Certifique-se de que **a Entrada** está selecionada e clique em Adicionar **Nova Regra**.
4. Dê à regra um nome descritivo, como "*In from AD – User DoNotSyncFilter*". Selecione a floresta correta, selecione **Utilizador** como o **tipo de objeto CS**, e selecione **Pessoa** como o tipo de **objeto MV**. No **Tipo de Ligação,** selecione **'Juntar-se'.** Em **Precedência,** escreva um valor que não seja atualmente utilizado por outra regra de sincronização (por exemplo 50), e depois clique em **Seguinte**.  
   ![Descrição de entrada 1](./media/how-to-connect-sync-configure-filtering/inbound1.png)  
5. No **filtro De scoping,** clique em **Adicionar Grupo**e clique em Adicionar **Cláusula**. No **Atributo**, selecione **ExtensionAttribute15**. Certifique-se de que **o Operador** está definido para **EQUAL**, e digite o valor **NoSync** na caixa **Valor.** Clique em **Seguinte**.  
   ![Inbound 2 âmbito](./media/how-to-connect-sync-configure-filtering/inbound2.png)  
6. Deixe as regras **de Junção** vazias e, em seguida, clique em **Seguinte**.
7. Clique **em Adicionar Transformação,** selecione o **FlowType** como **Constant**e selecione **cloudFiltered** como o **Atributo Alvo**. Na caixa de texto **Source,** **escreva True**. Clique **em Adicionar** para guardar a regra.  
   ![Transformação de entrada 3](./media/how-to-connect-sync-configure-filtering/inbound3.png)
8. Para completar a configuração, é necessário executar uma **sincronização completa**. Continuar a ler A secção [Aplicar e verificar alterações](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Filtragem positiva: "só sincroniza estes"
Expressar filtragem positiva pode ser mais desafiante porque também tem que considerar objetos que não são óbvios para serem sincronizados, como salas de conferências. Também vai anular o filtro predefinido na regra fora da caixa **In from AD - User Join**. Quando criar o seu filtro personalizado, certifique-se de que não inclui objetos críticos do sistema, objetos de conflito de replicação, caixas de correio especiais e contas de serviço para Azure AD Connect.

A opção de filtragem positiva requer duas regras de sincronização. É necessário uma regra (ou várias) com o âmbito correto dos objetos para sincronizar. Também precisa de uma segunda regra de sincronização que filtra todos os objetos que ainda não foram identificados como um objeto que deve ser sincronizado.

No exemplo seguinte, apenas sincroniza os objetos do utilizador onde o atributo do departamento tem o valor **Vendas.**

1. Inscreva-se no servidor que está a executar a sincronização do Azure AD Connect utilizando uma conta que é membro do grupo de segurança **ADSyncAdmins.**
2. Iniciar **o Editor de Regras** de Sincronização a partir do menu **Iniciar.**
3. Certifique-se de que **a Entrada** está selecionada e clique em Adicionar **Nova Regra**.
4. Dê à regra um nome descritivo, como "*In from AD – User Sales sync*". Selecione a floresta correta, selecione **Utilizador** como o **tipo de objeto CS**, e selecione **Pessoa** como o tipo de **objeto MV**. No **Tipo de Ligação,** selecione **'Juntar-se'.** Em **Precedência,** escreva um valor que não seja atualmente utilizado por outra regra de sincronização (por exemplo 51), e depois clique em **Seguinte**.  
   ![Entrada 4 descrição](./media/how-to-connect-sync-configure-filtering/inbound4.png)  
5. No **filtro De scoping,** clique em **Adicionar Grupo**e clique em Adicionar **Cláusula**. No **Atributo**, selecione **department**. Certifique-se de que o Operador está definido para **EQUAL**, e digite o valor **Vendas** na caixa **valor.** Clique em **Seguinte**.  
   ![Âmbito de entrada 5](./media/how-to-connect-sync-configure-filtering/inbound5.png)  
6. Deixe as regras **de Junção** vazias e, em seguida, clique em **Seguinte**.
7. Clique **em Adicionar Transformação,** selecione **Constant** como **FlowType**e selecione a **cloudFiltered** como o **Atributo Alvo**. Na caixa **'Fonte',** **escreva Falso**. Clique **em Adicionar** para guardar a regra.  
   ![Entrada 6 transformação](./media/how-to-connect-sync-configure-filtering/inbound6.png)  
   Este é um caso especial onde você explicitamente definir cloudFiltered to **False**.
8. Agora temos de criar a regra da sincronização de capturas. Dê à regra um nome descritivo, como "*In from AD – User Catch-all filter*". Selecione a floresta correta, selecione **Utilizador** como o **tipo de objeto CS**, e selecione **Pessoa** como o tipo de **objeto MV**. No **Tipo de Ligação,** selecione **'Juntar-se'.** Em **Precedência,** escreva um valor que não seja atualmente utilizado por outra Regra de Sincronização (por exemplo 99). Selecionou um valor de precedência mais elevado (precedência inferior) do que a regra de sincronização anterior. Mas também deixou algum espaço para que possa adicionar mais regras de sincronização de filtragem mais tarde, quando quiser começar a sincronizar departamentos adicionais. Clique em **Seguinte**.  
   ![Descrição de entrada 7](./media/how-to-connect-sync-configure-filtering/inbound7.png)  
9. Deixe **o filtro de escotagem** vazio e clique em **Seguinte**. Um filtro vazio indica que a regra deve ser aplicada a todos os objetos.
10. Deixe as regras **de Junção** vazias e, em seguida, clique em **Seguinte**.
11. Clique **em Adicionar Transformação,** selecione **Constant** como **o FlowType**e selecione **cloudFiltered** como o **Atributo Alvo**. Na caixa **'Fonte',** escreva **True**. Clique **em Adicionar** para guardar a regra.  
    ![Transformação de entrada 3](./media/how-to-connect-sync-configure-filtering/inbound3.png)  
12. Para completar a configuração, é necessário executar uma **sincronização completa**. Continuar a ler A secção [Aplicar e verificar alterações](#apply-and-verify-changes).

Se precisar, pode criar mais regras do primeiro tipo onde inclua mais objetos na sincronização.

### <a name="outbound-filtering"></a>Filtragem de saída
Em alguns casos, é necessário fazer a filtragem apenas depois de os objetos se terem juntado no metaverso. Por exemplo, pode ser necessário olhar para o atributo de correio da floresta de recursos, e o utilizadorPrincipalName atribui da floresta da conta, para determinar se um objeto deve ser sincronizado. Nestes casos, cria-se a filtragem da regra de saída.

Neste exemplo, altera-se a filtragem de modo a que apenas os utilizadores que tenham o seu nome de correio eletrónico e utilizador TopncipalName que @contoso.com terminam sejam sincronizados:

1. Inscreva-se no servidor que está a executar a sincronização do Azure AD Connect utilizando uma conta que é membro do grupo de segurança **ADSyncAdmins.**
2. Iniciar **o Editor de Regras** de Sincronização a partir do menu **Iniciar.**
3. De acordo com **o Tipo de Regras,** clique **em Outbound**.
4. Dependendo da versão do Connect que utiliza, encontre a regra nomeada **out to AZure AD – User Join** or out to **Azure AD - User Join SOAInAD**, e clique em **Editar**.
5. No pop-up, **responda Sim** para criar uma cópia da regra.
6. Na página **Descrição,** **altere a Precedência** para um valor não reutilizado, como 50.
7. Clique em **Filtrar** o filtro na navegação à esquerda e, em seguida, clique na **cláusula Adicionar**. No **Atributo**, selecione **correio.** No **Operador**, selecione **ENDSWITH**. In **Value**, type ** \@ contoso.com**e, em seguida, clique na **cláusula Adicionar**. No **Atributo**, selecione **userPrincipalName**. No **Operador**, selecione **ENDSWITH**. In **Value**, tipo ** \@ contoso.com**.
8. Clique em **Guardar**.
9. Para completar a configuração, é necessário executar uma **sincronização completa**. Continuar a ler A secção [Aplicar e verificar alterações](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Aplicar e verificar alterações
Depois de ter feito as alterações de configuração, deve aplicá-las aos objetos que já estão presentes no sistema. Pode também ser que os objetos que não estão atualmente no motor de sincronização devam ser processados (e o motor de sincronização precisa de voltar a ler o sistema de origem para verificar o seu conteúdo).

Se alterou a configuração utilizando a filtragem **de domínio** ou **unidade organizacional,** então tem de fazer uma **importação completa,** seguida da **sincronização Delta**.

Se alterar a configuração utilizando a filtragem **do atributo,** terá de fazer uma **sincronização completa**.

Faça os seguintes passos:

1. Iniciar **o Serviço de Sincronização** a partir do menu **Iniciar.**
2. Selecione **Conectores**. Na lista **de Conectores,** selecione o Conector onde fez uma alteração de configuração anteriormente. Em **Ações**, selecione **Run**.  
   ![Corrida de conector](./media/how-to-connect-sync-configure-filtering/connectorrun.png)  
3. Nos **perfis Run**, selecione a operação mencionada na secção anterior. Se precisar de executar duas ações, corra a segunda depois que a primeira terminar. (A coluna **do Estado** está **inativa** para o conector selecionado.)

Após a sincronização, todas as alterações serão encenadas para serem exportadas. Antes de fazer as alterações no Ad Azure, pretende verificar se todas estas alterações estão corretas.

1. Inicie um pedido de comando, e vá para `%ProgramFiles%\Microsoft Azure AD Sync\bin` .
2. Execute `csexport "Name of Connector" %temp%\export.xml /f:x`.  
   O nome do Conector está no Serviço de Sincronização. Tem um nome semelhante ao "contoso.com – Azure AD" para Azure AD.
3. Execute `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`.
4. Tem agora um ficheiro em %temperatura% nomeado export.csv que pode ser examinado no Microsoft Excel. Este ficheiro contém todas as alterações que estão prestes a ser exportadas.
5. Faça as alterações necessárias aos dados ou configuração, e execute estes passos novamente (Importar, Sincronizar e Verificar) até que as alterações que estão prestes a ser exportadas sejam o que espera.

Quando estiver satisfeito, exporte as alterações para Azure AD.

1. Selecione **Conectores**. Na lista **de Conectores,** selecione o Conector AD Azure. Em **Ações**, selecione **Run**.
2. Nos **perfis run**, selecione **Export**.
3. Se a sua configuração mudar eliminar muitos objetos, verá um erro na exportação quando o número é mais do que o limiar configurado (por defeito 500). Se vir este erro, terá de desativar temporariamente a função "[evitar eliminações acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md)".

Agora é hora de ativar o agendador novamente.

1. Iniciar **o Programador** de Tarefas a partir do menu **Iniciar.**
2. Diretamente na **Biblioteca do Programador de Tarefas,** encontre a tarefa chamada **Azure AD Sync Scheduler,** clique à direita e selecione **Enable**.

## <a name="group-based-filtering"></a>Filtragem baseada em grupo
Pode configurar a filtragem baseada em grupo na primeira vez que instala o Azure AD Connect utilizando [uma instalação personalizada](how-to-connect-install-custom.md#sync-filtering-based-on-groups). Destina-se a uma implantação piloto onde se quer apenas um pequeno conjunto de objetos a ser sincronizado. Quando desativa a filtragem baseada em grupo, não pode ser ativada novamente. Não é *suportado* para usar filtragem baseada em grupo numa configuração personalizada. Só é suportado para configurar esta funcionalidade utilizando o assistente de instalação. Quando completar o seu piloto, use uma das outras opções de filtragem neste tópico. Quando utilizar a filtragem baseada em OU em conjunto com a filtragem baseada em grupo, deve ser incluída a ou as ou as quais o grupo e os seus membros estão localizados.

Ao sincronizar várias florestas de AD, pode configurar a filtragem baseada em grupo especificando um grupo diferente para cada conector AD. Se desejar sincronizar um utilizador numa floresta de AD e o mesmo utilizador tiver um ou mais objetos correspondentes noutras florestas de AD, deve certificar-se de que o objeto do utilizador e todos os seus objetos correspondentes estão dentro do âmbito de filtragem baseado em grupo. Por exemplo:

* Você tem um utilizador em uma floresta que tem um objeto FSP correspondente (Foreign Security Principal) em outra floresta. Ambos os objetos devem estar dentro do âmbito de filtragem baseado em grupo. Caso contrário, o utilizador não será sincronizado com a Azure AD.

* Tem um utilizador numa floresta que tem uma conta de recursos correspondente (por exemplo, caixa de correio ligada) noutra floresta. Além disso, configura o Azure AD Connect para ligar o utilizador à conta de recursos. Ambos os objetos devem estar dentro do âmbito de filtragem baseado em grupo. Caso contrário, o utilizador não será sincronizado com a Azure AD.

* Tem um utilizador numa floresta que tem um contacto de correio correspondente noutra floresta. Além disso, configura o Azure AD Connect para ligar o utilizador ao contacto de correio. Ambos os objetos devem estar dentro do âmbito de filtragem baseado em grupo. Caso contrário, o utilizador não será sincronizado com a Azure AD.


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre a configuração [de sincronização Azure AD Connect.](how-to-connect-sync-whatis.md)
- Saiba mais sobre [a integração das suas identidades no local com a Azure AD.](whatis-hybrid-identity.md)
