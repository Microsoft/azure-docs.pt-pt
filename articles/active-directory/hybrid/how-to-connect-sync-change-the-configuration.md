---
title: 'Sincronização do Azure AD Connect: Fazer uma alteração na sincronização do Azure AD Connect de configuração | Documentos da Microsoft'
description: Explica como fazer uma alteração à configuração na sincronização do Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bf55cef00c0600963f8363e617c24fdc9113eae
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57543870"
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Sincronização do Azure AD Connect: Fazer uma alteração na configuração predefinida
O objetivo deste artigo é para orientá-lo como fazer alterações à configuração padrão na sincronização do Azure Active Directory (Azure AD) Connect. Ele fornece passos para alguns cenários comuns. Com esse conhecimento, deve ser capaz de fazer alterações simples em sua própria configuração com base em suas próprias regras de negócio.

> [!WARNING]
> Se fizer alterações às regras de sincronização padrão, em seguida, estas alterações serão substituídas na próxima vez que o Azure AD Connect é atualizado, resultando em resultados da sincronização indesejados inesperado e provavelmente.
>
> As regras de sincronização de out-of-box têm um thumbprint. Se fizer uma alteração para estas regras, o thumbprint já não é correspondentes. Talvez tenha problemas no futuro, quando tenta aplicar uma nova versão do Azure AD Connect. Apenas fazer alterações a forma como ele é descrito neste artigo.

## <a name="synchronization-rules-editor"></a>Editor de regras de sincronização
O Editor de regras de sincronização é utilizado para ver e alterar a configuração predefinida. Pode encontrá-lo sobre a **iniciar** menu no **do Azure AD Connect** grupo.  
![Menu Iniciar com o Editor de regras de sincronização](./media/how-to-connect-sync-change-the-configuration/startmenu2.png)

Quando abrir o editor, verá as regras de out-of-box predefinidas.

![Editor de regras de sincronização](./media/how-to-connect-sync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navegar no editor
Utilizar listas pendentes na parte superior do editor, pode encontrar rapidamente uma regra específica. Por exemplo, se quiser ver as regras em que está incluído o atributo proxyAddresses, pode alterar listas pendentes para o seguinte:  
![Filtragem de SRE](./media/how-to-connect-sync-change-the-configuration/filtering.png)  
Para repor a filtragem e carregar uma nova configuração, pressione F5 no teclado.

No canto superior direito é o **Adicionar nova regra** botão. Utilize este botão para criar sua própria regra personalizada.

Na parte inferior são botões para atuar numa regra de sincronização selecionado. **Editar** e **eliminar** fazer o que esperaria que eles. **Exportar** produz um script do PowerShell para voltar a criar a regra de sincronização. Com este procedimento, pode mover uma regra de sincronização de um servidor para outro.

## <a name="create-your-first-custom-rule"></a>Criar a sua primeira regra personalizada
As alterações mais comuns são os fluxos de atributos. Os dados no seu diretório de origem podem não ser os mesmos que no Azure AD. No exemplo nesta secção, certifique-se de que o nome de um usuário sempre se encontra em *capitalização adequada*.

### <a name="disable-the-scheduler"></a>Desativar o scheduler
O [agendador](how-to-connect-sync-feature-scheduler.md) é executado a cada 30 minutos por predefinição. Certifique-se de que não está a iniciar enquanto estiver a efetuar alterações e as novas regras de resolução de problemas. Para desativar temporariamente o scheduler, inicie o PowerShell e execute `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Desativar o scheduler](./media/how-to-connect-sync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Criar a regra
1. Clique em **Adicionar nova regra**.
2. Sobre o **Descrição** página, introduza o seguinte:  
   ![Regra de filtragem de entrada](./media/how-to-connect-sync-change-the-configuration/description2.png)  
   * **Nome**: Dê um nome descritivo à regra.
   * **Descrição**: Dê algum esclarecimento, para que alguém possa entender o que a regra destina-se.
   * **Ligado sistema**: Este é o sistema em que o objeto pode ser encontrado. Neste caso, selecione **conector do Active Directory**.
   * **Tipo de objeto de sistema/Metaverso ligado**: Selecione **usuário** e **pessoa**, respectivamente.
   * **Tipo de ligação**: Alterar este valor para **associar**.
   * **Precedência**: Forneça um valor que seja exclusivo no sistema. Um valor numérico inferior indica precedência superior.
   * **Tag**: Deixe vazio. Apenas as regras de out-of-box da Microsoft devem ter esta caixa preenchida com um valor.
3. Sobre o **filtro Scoping** página, introduza **givenName ISNOTNULL**.  
   ![Regra de filtro de âmbito de entrada](./media/how-to-connect-sync-change-the-configuration/scopingfilter.png)  
   Esta secção é usada para definir a quais objetos deve aplicar a regra. Se for deixado vazio, a regra seria aplicada a todos os objetos de utilizador. No entanto, que incluem salas de conferência, contas de serviço e outros objetos de utilizador não pessoas.
4. Sobre o **associar regras** página, deixar o campo vazio.
5. Sobre o **transformações** página, alterar **FlowType** para **expressão**. Para **atributo Target**, selecione **givenName**. E para **origem**, introduza **PCase([givenName])**.
   ![Transformações de regra de entrada](./media/how-to-connect-sync-change-the-configuration/transformations.png)  
   O motor de sincronização diferencia maiúsculas de minúsculas para o nome da função e o nome do atributo. Se digitar algo errado, verá um aviso ao adicionar a regra. Pode guardar e continuar, mas precisa para reabrir e corrija a regra.
6. Clique em **adicionar** para guardar a regra.

Nova regra personalizada deve estar visível com outras regras de sincronização no sistema.

### <a name="verify-the-change"></a>Verificar a alteração
Com esta nova alteração, deseja certificar-se de que ele está a funcionar conforme esperado e que não está a gerar erros. Dependendo do número de objetos que tiver, existem duas formas de efetuar este passo:

- Execute uma sincronização completa em todos os objetos.
- Execute uma pré-visualização e a sincronização completa num único objeto.

Abra o **serviço de sincronização** da **iniciar** menu. Os passos nesta secção estão todos nesta ferramenta.

**Sincronização completa em todos os objetos**  

   1. Selecione **conectores** na parte superior. Identifique o conector que alterou na secção anterior (no caso, serviços de domínio do Active Directory) e selecioná-lo. 
   2. Para **ações**, selecione **executar**.
   3. Selecione **sincronização completa**e, em seguida, selecione **OK**.
   ![Sincronização completa](./media/how-to-connect-sync-change-the-configuration/fullsync.png)  
   Os objetos são atualizados no metaverso. Verifique se as alterações ao examinar o objeto no metaverso.

**Pré-visualização e uma sincronização completa num único objeto**  

   1. Selecione **conectores** na parte superior. Identifique o conector que alterou na secção anterior (no caso, serviços de domínio do Active Directory) e selecioná-lo.
   2. Selecione **procurar espaço conector**. 
   3. Utilizar **âmbito** para localizar um objeto que pretende utilizar para testar a alteração. Selecione o objeto e clique em **pré-visualização**. 
   4. No ecrã novo, selecione **consolidar pré-visualização**.  
   ![Confirmar pré-visualização](./media/how-to-connect-sync-change-the-configuration/commitpreview.png)  
   A alteração agora está empenhada em metaverso.

**Ver o objeto no metaverso**  

1. Escolha alguns objetos de exemplo para se certificar de que o valor é esperado e que a regra é aplicada. 
2. Selecione **pesquisa de Metaverso** da parte superior. Adicione qualquer filtro que precisa localizar os objetos relevantes. 
3. A partir do resultado da pesquisa, abra um objeto. Ver os valores de atributo e também verificar na **regras de sincronização** coluna que a regra aplicada como esperado.  
![Pesquisa de metaversos](./media/how-to-connect-sync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Ativar o scheduler
Se tudo estiver conforme esperado, pode habilitar o agendador novamente. A partir do PowerShell, execute `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Outras alterações de fluxo de atributos comuns
A seção anterior descreveu como fazer alterações num fluxo de atributos. Nesta secção, são fornecidos alguns exemplos adicionais. Os passos para saber como criar a regra de sincronização é abreviada, mas pode encontrar os passos completos na secção anterior.

### <a name="use-an-attribute-other-than-the-default"></a>Usar um atributo diferente da predefinição
Neste cenário da Fabrikam, há uma floresta em que o alfabeto local é utilizado para o nome próprio, apelido e nome a apresentar. A representação de carateres latinos desses atributos pode ser encontrada nos atributos de extensão. Para a criação de um endereço global listar no Azure AD e do Office 365, a organização deseja usar esses atributos em vez disso.

Com uma configuração padrão, um objeto da floresta local fica assim:  
![Fluxo de atributos 1](./media/how-to-connect-sync-change-the-configuration/attributeflowjp1.png)

Para criar uma regra com outros fluxos de atributos, faça o seguinte:

1. Abra o **Editor de regras de sincronização** da **iniciar** menu.
2. Com o **Inbound** ainda selecionada para a esquerda, clique nas **Adicionar nova regra** botão.
3. Dê a regra de um nome e descrição. Selecione a instância do Active Directory no local e os tipos de objeto relevantes. Na **tipo de ligação**, selecione **associar**. Para **precedência**, escolha um número que não é utilizado por outra regra. As regras de out-of-box começam com 100, para que o valor de 50 pode ser utilizado neste exemplo.
  ![Fluxo de atributos 2](./media/how-to-connect-sync-change-the-configuration/attributeflowjp2.png)
4. Deixe **Scoping filtro** vazio. (Ou seja, ele deve ser aplicada a todos os objetos de utilizador na floresta.)
5. Deixe **associar regras** vazio. (Ou seja, permitir que a regra de out-of-box lidar com qualquer associações.)
6. Na **transformações**, criar os seguintes fluxos:  
  ![Fluxo de atributos 3](./media/how-to-connect-sync-change-the-configuration/attributeflowjp3.png)
7. Clique em **adicionar** para guardar a regra.
8. Aceda a **Synchronization Service Manager**. No **conectores**, seleccione o conector onde adicionou a regra. Selecione **execute**e, em seguida, selecione **sincronização completa**. Uma sincronização completa recalcula todos os objetos ao utilizar as regras atuais.

Este é o resultado para o mesmo objeto com esta regra personalizada:  
![Fluxo de atributos 4](./media/how-to-connect-sync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Comprimento de atributos
Atributos de cadeia de caracteres são indexáveis por padrão, e o comprimento máximo é de 448 caracteres. Se estiver a trabalhar com atributos de cadeia de caracteres que podem conter mais, certifique-se incluir o seguinte no fluxo de atributos:  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>Alterar o userPrincipalSuffix
O atributo userPrincipalName no Active Directory não é sempre conhecido pelos utilizadores e pode não ser adequado que o ID de início de sessão. Com o Assistente de instalação de sincronização do Azure AD Connect, pode escolher um atributo diferente – por exemplo, *correio*. Mas em alguns casos, o atributo deve ser calculado.

Por exemplo, a empresa Contoso tem dois diretórios do Azure AD, uma para produção e uma para testes. Eles querem que os utilizadores no seu inquilino de teste para utilizar outro sufixo no ID de início de sessão:  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

Nesta expressão, levar tudo esquerdo dos primeiros @-sign (Word) e concatenate com uma cadeia de caracteres fixa.

### <a name="convert-a-multi-value-attribute-to-single-value"></a>Converter um atributo com múltiplos valor de valor único
Alguns atributos no Active Directory estão com múltiplos valores no esquema, mesmo que eles procuram o valor único no Active Directory utilizadores e computadores. Um exemplo é o atributo de descrição:  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

Nesta expressão, se o atributo tem um valor, levar o primeiro item (*Item*) no atributo, remover espaços à esquerda e (*Trim*) e, em seguida, mantenha os primeiro 448 caracteres (*esquerda* ) na cadeia de caracteres.

### <a name="do-not-flow-an-attribute"></a>Não fluxo um atributo
Para informações gerais sobre o cenário para nesta seção, consulte [controlar o processo de fluxo de atributo](concept-azure-ad-connect-sync-declarative-provisioning.md#control-the-attribute-flow-process).

Existem duas formas de fluxo não de um atributo. A primeira é utilizando o Assistente de instalação para [remover atributos selecionados](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering). Esta opção funciona se nunca tiver sincronizado o atributo antes. No entanto, se tiver começado a sincronizar este atributo e removê-lo mais tarde com esta funcionalidade, o pára de motor de sincronização gerenciamento atributo e os valores existentes é deixado no Azure AD.

Se pretender remover o valor de um atributo e certifique-se de que não flua no futuro, precisa de criar uma regra personalizada.

Neste cenário da Fabrikam, podemos ter percebido que alguns dos atributos que sincronizar para a cloud não devem estar lá. Queremos certificar-se de que esses atributos são removidos do Azure AD.  
![Atributos de extensão incorreto](./media/how-to-connect-sync-change-the-configuration/badextensionattribute.png)

1. Criar uma nova regra de sincronização de entrada e preencha a descrição.
  ![Descrições](./media/how-to-connect-sync-change-the-configuration/syncruledescription.png)
2. Criar fluxos de atributos com **expressão** para **FlowType** e com **AuthoritativeNull** para **origem**. O literal **AuthoritativeNull** indica que o valor deve estar vazio no metaverso, mesmo que uma regra de sincronização de precedência inferior tenta popular o valor.
  ![Transformação para atributos de extensão](./media/how-to-connect-sync-change-the-configuration/syncruletransformations.png)
3. Guarde a regra de sincronização. Iniciar o **serviço de sincronização**, localize o conector, selecione **execute**e, em seguida, selecione **sincronização completa**. Este passo recalcula todos os fluxos de atributos.
4. Certifique-se de que as alterações pretendidas estão prestes a ser exportado ao pesquisar o espaço conector.
  ![Eliminação em etapas](./media/how-to-connect-sync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Criar regras com o PowerShell
Usando o editor de regra de sincronização funciona bem quando tem apenas algumas alterações para fazer. Se precisar de fazer muitas alterações, o PowerShell pode ser uma opção melhor. Alguns recursos avançados só estão disponíveis com o PowerShell.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Obter o script do PowerShell para uma regra de out-of-box
Para ver o PowerShell script que criou uma regra de out-of-box, selecione a regra no editor de regras de sincronização e clique em **exportar**. Esta ação dá-lhe o script do PowerShell que criou a regra.

### <a name="advanced-precedence"></a>Precedência avançada
As regras de sincronização de out-of-box começam com um valor de precedência de 100. Se tiver várias florestas, e precisa fazer muitas alterações personalizadas, em seguida, regras de sincronização de 99 podem não ser suficiente.

Pode instruir o motor de sincronização que pretende que as regras adicionais inseridas antes das regras de out-of-box. Para obter esse comportamento, siga estes passos:

1. Marcar a primeira regra de sincronização de out-of-box (**de associação de utilizador do AD**) no editor de regras de sincronização e selecione **exportar**. Copie o valor do identificador do SR.  
![PowerShell antes da alteração](./media/how-to-connect-sync-change-the-configuration/powershell1.png)  
2. Crie a nova regra de sincronização. Pode usar o editor de regras de sincronização para criá-lo. Exporte a regra para um script do PowerShell.
3. Na propriedade **PrecedenceBefore**, insira o valor do identificador da regra de out-of-box. Definir o **precedência** ao **0**. Certificar-se de que o atributo de identificador é exclusivo e que não está a reutilizar um GUID em outra regra. Também certificar-se de que o **ImmutableTag** propriedade não está definida. Esta propriedade deve ser definida apenas para uma regra de out-of-box.
4. Guarde o script do PowerShell e executá-lo. O resultado é que a regra personalizada é atribuída o valor de precedência de 100 e todas as outras regras de out-of-box são incrementadas.  
![PowerShell após a alteração](./media/how-to-connect-sync-change-the-configuration/powershell2.png)  

Pode ter muitas regras de sincronização personalizados utilizando o mesmo **PrecedenceBefore** valor quando necessário.

## <a name="enable-synchronization-of-usertype"></a>Ativar a sincronização de UserType
Azure AD Connect suporta a sincronização do **UserType** atributo **utilizador** objetos na versão 1.1.524.0 e mais tarde. Mais especificamente, foram introduzidas as seguintes alterações:

- O esquema do tipo de objeto **utilizador** no conector do Azure AD é expandida para incluir o atributo UserType, que é do tipo de cadeia e é o valor único.
- O esquema do tipo de objeto **pessoa** no metaverso é expandido para incluir o atributo UserType, que é do tipo de cadeia e é o valor único.

Por predefinição, o atributo UserType não está ativado para a sincronização porque não existe nenhum atributo UserType correspondente no Active Directory no local. Tem de ativar manualmente a sincronização. Antes de fazer isso, tem de tome nota do seguinte comportamento imposta pelo Azure AD:

- O Azure AD só aceita dois valores para o atributo UserType: **Membro** e **convidado**.
- Se o atributo UserType não está ativado para a sincronização no Azure AD Connect, utilizadores do Azure AD criados através da sincronização de diretórios teria o atributo UserType definido como **membro**.
- O Azure AD não permite que o atributo UserType em utilizadores do Azure AD existentes ser alterado pelo Azure AD Connect. Só pode ser definido durante a criação dos utilizadores do Azure AD.

Antes de ativar a sincronização do atributo UserType, primeiro deve decidir como o atributo é derivado do Active Directory no local. Seguem-se as abordagens mais comuns:

- Designar uma não utilizado no local atributo de AD (por exemplo, extensionAttribute1) a ser utilizado como o atributo de origem. Designado no local atributo AD deve ser do tipo **cadeia de caracteres**, ter um único valor e conter o valor **membro** ou **convidado**. 

    Se escolher esta abordagem, tem de assegurar que o atributo designado é preenchido com o valor correto para todos os utilizadores objetos existentes no Active Directory no local que são sincronizados com o Azure AD antes de ativar a sincronização do atributo UserType .

- Em alternativa, pode derivar o valor para o atributo UserType de outras propriedades. Por exemplo, pretende sincronizar todos os utilizadores como **convidado** se suas instalações atributo userPrincipalName do AD acaba com parte do domínio <em>@partners.fabrikam123.org</em>. 

    Como mencionado anteriormente, o Azure AD Connect não permite que o atributo UserType em utilizadores do Azure AD existentes ser alterado pelo Azure AD Connect. Por conseguinte, tem de garantir que a lógica que tenha decidido é consistente com como o atributo UserType já está configurado para todos os utilizadores do Azure AD existentes no seu inquilino.

Os passos para ativar a sincronização do atributo UserType podem ser resumidos como:

1.  Desativar o agendador de sincronização e certifique-se de que não existe nenhuma sincronização em curso.
2.  Adicione o atributo de origem com o local do esquema do conector do AD.
3.  Adicione o UserType ao esquema do conector do Azure AD.
4.  Crie uma regra de sincronização de entrada para o fluxo o valor do atributo do Active Directory no local.
5.  Crie uma regra de sincronização de saída para o fluxo o valor do atributo para o Azure AD.
6.  Execute um ciclo de sincronização completa.
7.  Ative o agendador de sincronização.

>[!NOTE]
> O restante desta seção aborda estes passos. Estes modelos são descritos no contexto de uma implementação do Azure AD com a topologia de floresta única e sem as regras de sincronização personalizado. Se tiver uma topologia de várias floresta, as regras de sincronização personalizado configurado ou tem um servidor de teste, terá de ajustar as etapas apropriadamente.

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Passo 1: Desativar o agendador de sincronização e certifique-se de que não existe nenhuma sincronização em curso
Para evitar alterações indesejadas a exportar para o Azure AD, certifique-se de que não ocorre sincronização ocorre enquanto estiver no meio de a atualizar as regras de sincronização. Para desativar o agendador de sincronização interna:

 1. Inicie uma sessão do PowerShell no servidor do Azure AD Connect.
 2. Desativar a sincronização agendada, executando o cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 3. Abra o Synchronization Service Manager da **começar** > **serviço de sincronização**.
 4. Vá para o **Operations** separador e confirme que não existe nenhuma operação com o estado *em curso*.

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>Passo 2: Adicione o atributo de origem com o local do esquema de conector AD
Nem todos os atributos do Azure AD são importados para local espaço conector do AD. Para adicionar o atributo de origem para a lista dos atributos importados:

 1. Vá para o **conectores** separador o Synchronization Service Manager.
 2. Conector do AD de botão direito do mouse no local e selecione **propriedades**.
 3. Na caixa de diálogo pop-up, vá para o **selecionar atributos** separador.
 4. Certifique-se de que o atributo de origem está selecionado na lista de atributos.
 5. Clique em **OK** para guardar.
![Adicionar o atributo de origem para o local esquema de conector AD](./media/how-to-connect-sync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-to-the-azure-ad-connector-schema"></a>Passo 3: Adicionar o UserType ao esquema do conector do Azure AD
Por predefinição, o atributo UserType não é importado para o espaço de ligar-se de AD do Azure. Para adicionar o atributo UserType à lista de atributos importados:

 1. Vá para o **conectores** separador o Synchronization Service Manager.
 2. Com o botão direito a **conector do Azure AD** e selecione **propriedades**.
 3. Na caixa de diálogo pop-up, vá para o **selecionar atributos** separador.
 4. Certifique-se de que o atributo UserType está selecionado na lista de atributos.
 5. Clique em **OK** para guardar.

![Adicionar o atributo de origem para o esquema do conector do Azure AD](./media/how-to-connect-sync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Passo 4: Criar uma regra de sincronização de entrada para o fluxo o valor do atributo do Active Directory no local
A regra de sincronização de entrada que permite que o valor do atributo para o fluxo a partir do atributo de origem do Active Directory no local para o metaverso:

1. Abra o Editor de regras de sincronização da **começar** > **Editor de regras de sincronização**.
2. Definir o filtro de pesquisa **direção** ser **entrada**.
3. Clique nas **Adicionar nova regra** botão para criar uma nova regra de entrada.
4. Sob o **Descrição** separador, forneça a seguinte configuração:

    | Atributo | Value | Detalhes |
    | --- | --- | --- |
    | Name | *Forneça um nome* | Por exemplo, *do AD – UserType do utilizador* |
    | Descrição | *Forneça uma descrição* |  |
    | Sistema ligado | *Escolha os locais conector AD* |  |
    | Tipo de objeto de sistema ligado | **Utilizador** |  |
    | Tipo de objeto de Metaverso | **Pessoa** |  |
    | Tipo de Ligação | **Associar** |  |
    | Precedência | *Escolha um número entre 1 a 99* | 1 a 99 está reservado para regras de sincronização personalizados. Não escolha um valor que é utilizado por outra regra de sincronização. |

5. Vá para o **filtro Scoping** separador e adicione um **único grupo de filtro de âmbito** com a cláusula seguinte:

    | Atributo | Operador | Value |
    | --- | --- | --- |
    | adminDescription | NOTSTARTWITH | Utilizador\_ |

    O filtro de âmbito determina que no local a AD objetos que esta regra de sincronização de entrada é aplicada. Neste exemplo, utilizamos o filtro de âmbito mesmo utilizado no *do AD – utilizador comuns* regra de sincronização de out-of-box, o que impede que a regra de sincronização a ser aplicada a objetos de utilizador criados através do utilizador do Azure AD funcionalidade de repetição de escrita. Poderá ter de otimizar o filtro de âmbito, de acordo com a implementação do Azure AD Connect.

6. Vá para o **transformação** separador e implementar a regra de transformação pretendida. Por exemplo, se tiver designado um não utilizado no local atributo de AD (por exemplo, extensionAttribute1) como o atributo de origem para o UserType, pode implementar um fluxo de atributos direto:

    | Tipo de fluxo | Atributo de destino | Origem | Aplicar uma vez | Tipo de intercalação |
    | --- | --- | --- | --- | --- |
    | Direto | UserType | extensionAttribute1 | Desmarcada | Atualizar |

    Noutro exemplo, pretende derivar o valor para o atributo UserType de outras propriedades. Por exemplo, pretende sincronizar todos os utilizadores como convidado se suas instalações atributo userPrincipalName do AD acaba com parte do domínio <em>@partners.fabrikam123.org</em>. Pode implementar uma expressão como esta:

    | Tipo de fluxo | Atributo de destino | Origem | Aplicar uma vez | Tipo de intercalação |
    | --- | --- | --- | --- | --- |
    | Expressão | UserType | IIf(IsPresent([userPrincipalName]),IIF(CBool(Instr(LCase([userPrincipalName]),"@partners.fabrikam123.org")=0), "Membro", "Convidado"), erro ("UserPrincipalName não está presente para determinar o UserType")) | Desmarcada | Atualizar |

7. Clique em **adicionar** para criar a regra de entrada.

![Criar regra de sincronização de entrada](./media/how-to-connect-sync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Passo 5: Criar uma regra de sincronização de saída para o fluxo o valor do atributo para o Azure AD
A regra de sincronização de saída permite que o valor do atributo a ser enviados do metaverse para o atributo UserType no Azure AD:

1. Vá para o Editor de regras de sincronização.
2. Definir o filtro de pesquisa **direção** ser **saída**.
3. Clique nas **Adicionar nova regra** botão.
4. Sob o **Descrição** separador, forneça a seguinte configuração:

    | Atributo | Value | Detalhes |
    | ----- | ------ | --- |
    | Name | *Forneça um nome* | Por exemplo, *expansão para AAD – UserType do utilizador* |
    | Descrição | *Forneça uma descrição* ||
    | Sistema ligado | *Seleccione o conector AAD* ||
    | Tipo de objeto de sistema ligado | **Utilizador** ||
    | Tipo de objeto de Metaverso | **Pessoa** ||
    | Tipo de Ligação | **Associar** ||
    | Precedência | *Escolha um número entre 1 a 99* | 1 a 99 está reservado para regras de sincronização personalizados. Não escolha um valor que é utilizado por outra regra de sincronização. |

5. Vá para o **filtro Scoping** separador e adicione um **único grupo de filtro de âmbito** com dois cláusulas:

    | Atributo | Operador | Value |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Utilizador |
    | cloudMastered | NOTEQUAL | Verdadeiro |

    O filtro de âmbito determina a que a do Azure AD objetos que esta regra de sincronização de saída é aplicada. Neste exemplo, usamos o mesmo filtro de âmbito do *fora para o AD – a identidade do usuário* regra de sincronização de out-of-box. Ela impede que a regra de sincronização a ser aplicada a objetos de utilizador que não estão sincronizados do Active Directory no local. Poderá ter de otimizar o filtro de âmbito, de acordo com a implementação do Azure AD Connect.

6. Vá para o **transformação** separador e implementar a seguinte regra de transformação:

    | Tipo de fluxo | Atributo de destino | Origem | Aplicar uma vez | Tipo de intercalação |
    | --- | --- | --- | --- | --- |
    | Direto | UserType | UserType | Desmarcada | Atualizar |

7. Clique em **adicionar** para criar a regra de saída.

![Criar regra de sincronização de saída](./media/how-to-connect-sync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>Passo 6: Executar um ciclo de sincronização completa
Em geral, um ciclo de sincronização completa é necessário porque estamos tiver adicionado novos atributos em esquemas do Active Directory e o conector do Azure AD e introduziu as regras de sincronização personalizado. Deve verificar as alterações antes de exportá-los para o Azure AD. 

Pode utilizar os seguintes passos para verificar as alterações ao executar manualmente as etapas que formam um ciclo de sincronização completa.

1. Executar uma **importação completa** sobre o **conector do AD no local**:

   1. Vá para o **operações** separador o Synchronization Service Manager.
   2. Com o botão direito a **conector do AD no local** e selecione **executar**.
   3. Na caixa de diálogo pop-up, selecione **importação completa** e, em seguida, clique em **OK**.
   4. Aguarde a conclusão da operação.

    > [!NOTE]
    > Pode ignorar uma importação completa no local, conector AD Se o atributo de origem já está incluído na lista de importados de atributos. Em outras palavras, não era necessário que efetuar quaisquer alterações durante [passo 2: Adicione o atributo de origem com o local do esquema de conector AD](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Executar uma **importação completa** sobre o **conector do Azure AD**:

   1. Com o botão direito a **conector do Azure AD** e selecione **executar**.
   2. Na caixa de diálogo pop-up, selecione **importação completa** e, em seguida, clique em **OK**.
   3. Aguarde a conclusão da operação.

3. Verificar as alterações de regra de sincronização num objeto de utilizador existente:

    O atributo source dos locais foram importados do Active Directory e o UserType do Azure AD para seus respectivos espaços de conector. Antes de continuar com uma sincronização completa, fazer uma **pré-visualização** sobre um utilizador existente de objeto no local espaço conector do AD. O objeto que selecionou deve ter o atributo de origem preenchido.
    
    Êxito **pré-visualização** com UserType preenchido no metaverso é um bom indicador de que configurou a sincronização de regras corretamente. Para obter informações sobre como fazer uma **pré-visualização**, consulte a seção [verificar a alteração](#verify-the-change).

4. Executar um **completa de sincronização** sobre o **conector do AD no local**:

   1. Com o botão direito a **conector do AD no local** e selecione **executar**.
   2. Na caixa de diálogo pop-up, selecione **sincronização completa** e, em seguida, clique em **OK**.
   3. Aguarde a conclusão da operação.

5. Certifique-se **exportações pendentes** para o Azure AD:

   1. Com o botão direito a **conector do Azure AD** e selecione **procurar espaço conector**.
   2. Na **procurar espaço conector** caixa de diálogo pop-up:

      - Definir **âmbito** ao **pendentes exportação**.
      - Selecione todas as três caixas de verificação: **Adicione**, **modificar**, e **eliminar**.
      - Clique nas **pesquisa** botão para obter a lista de objetos com as alterações para ser exportada. Para examinar as alterações para um determinado objeto, faça duplo clique o objeto.
      - Certifique-se de que as alterações esperadas.

6. Execute **exportar** sobre o **conector do Azure AD**:

   1. Com o botão direito a **conector do Azure AD** e selecione **executar**.
   2. Na **executar o conector** caixa de diálogo pop-up, selecione **exportar** e, em seguida, clique em **OK**.
   3. Aguarde que a exportação para o Azure AD para concluir.

> [!NOTE]
> Estes passos não incluem a sincronização completa e exportar os passos no conector do Azure AD. Estes passos não são necessários porque os valores de atributos estão a fluir do Active Directory no local para o Azure AD apenas.

### <a name="step-7-re-enable-the-sync-scheduler"></a>Passo 7: Volte a ativar o agendador de sincronização
Volte a ativar o agendador de sincronização interna:

1. Inicie uma sessão do PowerShell.
2. Volte a ativar a sincronização agendada, executando o cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $true`.


## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre o modelo de configuração [aprovisionamento declarativo da compreensão](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Saiba mais sobre a linguagem de expressão no [Noções básicas sobre expressões de aprovisionamento declarativas](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

**Tópicos de descrição geral**

* [Sincronização do Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
