---
title: 'Azure AD Connect: Configurar o local de dados preferencial para os recursos do Office 365'
description: Descreve como colocar os recursos de usuário do Office 365 próximos ao usuário com Azure Active Directory Connect sincronização.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50cb5a76c6b19668fc23147244d65a0d996ebf90
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033725"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Sincronização de Azure Active Directory Connect: Configurar o local de dados preferencial para os recursos do Office 365
A finalidade deste tópico é orientá-lo sobre como configurar o atributo para o local de dados preferencial na sincronização do Azure Active Directory (Azure AD) Connect. Quando alguém usa recursos de várias regiões geográficas no Office 365, você usa esse atributo para designar a localização geográfica dos dados do Office 365 do usuário. (Os termos *região* e *geografia* são usados de maneira intercambiável.)

## <a name="enable-synchronization-of-preferred-data-location"></a>Habilitar a sincronização do local de dados preferencial
Por padrão, os recursos do Office 365 para seus usuários estão localizados na mesma área geográfica que o seu locatário do Azure AD. Por exemplo, se seu locatário estiver localizado em América do Norte, as caixas de correio do Exchange do usuário também estarão localizadas em América do Norte. Para uma organização multinacional, isso pode não ser o ideal.

Ao definir o atributo **preferredDataLocation**, você pode definir a área geográfica de um usuário. Você pode ter os recursos do Office 365 do usuário, como a caixa de correio e o OneDrive, na mesma área geográfica que o usuário, e ainda ter um locatário para toda a organização.

> [!IMPORTANT]
> Várias regiões geográficas estão disponíveis atualmente para clientes com um Enterprise Agreement ativo e um mínimo de 500 assinaturas de serviços do Office 365. Entre em contato com seu representante da Microsoft para obter detalhes.
>
>

Uma lista de todos os áreas geográficas para Office 365 pode ser encontrada em [onde os dados estão localizados?](https://aka.ms/datamaps).

Os áreas geográficas no Office 365 disponíveis para várias regiões geográficas são:

| Geo | valor de preferredDataLocation |
| --- | --- |
| Ásia-Pacífico | APC |
| Austrália | HORA |
| Canadá | PODEM |
| União Europeia | EUR |
| França | FRA |
| Índia | LOCALIZAR |
| Japão | JPN |
| Coreia | KOR |
| África do Sul | ZAF |
| Emirados Árabes Unidos | SEJA |
| Reino Unido | GBR |
| Estados Unidos | VIETNÃ |

* Se uma área geográfica não estiver listada nesta tabela (por exemplo, América do Sul), ela não poderá ser usada para várias regiões geográficas.

* Nem todas as cargas de trabalho do Office 365 dão suporte ao uso da configuração de uma área geográfica do usuário.

### <a name="azure-ad-connect-support-for-synchronization"></a>Suporte Azure AD Connect para sincronização

Azure AD Connect dá suporte à sincronização do atributo **preferredDataLocation** para objetos de **usuário** na versão 1.1.524.0 e posterior. Especificamente:

* O esquema do tipo de objeto **usuário** no Azure ad Connector é estendido para incluir o atributo **preferredDataLocation** . O atributo é do tipo, Cadeia de caracteres de valor único.
* O esquema do tipo de objeto **Person** no metaverso é estendido para incluir o atributo **preferredDataLocation** . O atributo é do tipo, Cadeia de caracteres de valor único.

Por padrão, o **preferredDataLocation** não está habilitado para sincronização. Esse recurso destina-se a organizações maiores. Você também deve identificar um atributo para manter a localização geográfica do Office 365 para seus usuários, porque não há nenhum atributo **preferredDataLocation** no local Active Directory. Isso será diferente para cada organização.

> [!IMPORTANT]
> O Azure AD permite que o atributo **preferredDataLocation** em **objetos de usuário de nuvem** seja configurado diretamente usando o PowerShell do Azure AD. O Azure AD não permite mais que o atributo **preferredDataLocation** em **objetos de usuário sincronizados** seja diretamente configurado usando o PowerShell do Azure AD. Para configurar esse atributo em **objetos de usuário sincronizados**, você deve usar Azure ad Connect.

Antes de habilitar a sincronização:

* Decida qual atributo de Active Directory local será usado como o atributo de origem. Deve ser do tipo, Cadeia de **caracteres de valor único**. Nas etapas a seguir, uma das extensões de **extensão** é usada.
* Se você tiver configurado anteriormente o atributo **preferredDataLocation** em **objetos de usuário sincronizados** existentes no Azure ad usando o PowerShell do Azure AD, deverá backport os valores de atributo para os objetos de **usuário** correspondentes no Active Directory local.

    > [!IMPORTANT]
    > Se você não backport esses valores, Azure AD Connect removerá os valores de atributo existentes no Azure AD quando a sincronização para o atributo **preferredDataLocation** estiver habilitada.

* Configure o atributo de origem em pelo menos alguns Active Directory objetos de usuário locais agora. Você pode usar isso para verificação mais tarde.

As seções a seguir fornecem as etapas para habilitar a sincronização do atributo **preferredDataLocation** .

> [!NOTE]
> As etapas são descritas no contexto de uma implantação do Azure AD com topologia de floresta única e sem regras de sincronização personalizadas. Se você tiver uma topologia de várias florestas, regras de sincronização personalizadas configuradas ou tiver um servidor de preparo, você deverá ajustar as etapas adequadamente.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Passo 1: Desabilitar o Agendador de sincronização e verificar se não há nenhuma sincronização em andamento
Para evitar que alterações não intencionais sejam exportadas para o Azure AD, certifique-se de que nenhuma sincronização ocorra enquanto você estiver no meio da atualização de regras de sincronização. Para desabilitar o Agendador de sincronização interno:

1. Inicie uma sessão do PowerShell no servidor de Azure AD Connect.
2. Desabilite a sincronização agendada executando este `Set-ADSyncScheduler -SyncCycleEnabled $false`cmdlet:.
3. Inicie o **Synchronization Service Manager** acessando **Iniciar** > **serviço de sincronização**.
4. Selecione a guia **operações** e confirme se não há nenhuma operação com o status *em andamento*.

![Captura de tela de Synchronization Service Manager](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>Passo 2: Adicionar o atributo de origem ao esquema do conector de Active Directory local
Nem todos os atributos do Azure AD são importados para o espaço do conector de Active Directory local. Se você tiver optado por usar um atributo que não é sincronizado por padrão, será necessário importá-lo. Para adicionar o atributo de origem à lista dos atributos importados:

1. Selecione a guia conectores na Synchronization Service Manager.
2. Clique com o botão direito do mouse no conector de Active Directory local e selecione **Propriedades**.
3. Na caixa de diálogo pop-up, vá para a guia **Selecionar atributos** .
4. Verifique se o atributo de origem selecionado para uso está marcado na lista de atributos. Se você não vir seu atributo, marque a caixa de seleção **Mostrar tudo** .
5. Para salvar, selecione **OK**.

![Captura de tela da caixa de diálogo Synchronization Service Manager e propriedades](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Passo 3: Adicionar **preferredDataLocation** ao esquema do Azure ad Connector
Por padrão, o atributo **preferredDataLocation** não é importado para o espaço do conector do Azure AD. Para adicioná-lo à lista de atributos importados:

1. Selecione a guia conectores na Synchronization Service Manager.
2. Clique com o botão direito do mouse no Azure AD Connector e selecione **Propriedades**.
3. Na caixa de diálogo pop-up, vá para a guia **Selecionar atributos** .
4. Selecione o atributo **preferredDataLocation** na lista.
5. Para salvar, selecione **OK**.

![Captura de tela da caixa de diálogo Synchronization Service Manager e propriedades](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-4-create-an-inbound-synchronization-rule"></a>Passo 4: Criar uma regra de sincronização de entrada
A regra de sincronização de entrada permite que o valor do atributo flua do atributo de origem no Active Directory local para o metaverso.

1. Inicie o **Editor de regras de sincronização** acessando **Iniciar** > **Editor de regras de sincronização**.
2. Defina a **direção** do filtro de pesquisa como **entrada**.
3. Para criar uma nova regra de entrada, selecione **Adicionar nova regra**.
4. Na guia **Descrição** , forneça a seguinte configuração:

    | Atributo | Value | Detalhes |
    | --- | --- | --- |
    | Name | *Forneça um nome* | Por exemplo, "entrada do AD – usuário preferredDataLocation" |
    | Descrição | *Forneça uma descrição personalizada* |  |
    | Sistema conectado | *Escolha o conector de Active Directory local* |  |
    | Tipo de objeto do sistema conectado | **Usuário** |  |
    | Tipo de objeto do metaverso | **Pessoa** |  |
    | Tipo de Ligação | **Associar** |  |
    | Precedência | *Escolha um número entre 1 e 99* | 1 a 99 é reservado para regras de sincronização personalizadas. Não escolha um valor que seja usado por outra regra de sincronização. |

5. Mantenha o **filtro de escopo** vazio, para incluir todos os objetos. Talvez seja necessário ajustar o filtro de escopo de acordo com sua implantação de Azure AD Connect.
6. Vá para a **guia transformação**e implemente a seguinte regra de transformação:

    | Tipo de fluxo | Atributo de destino | Source | Aplicar uma vez | Tipo de mesclagem |
    | --- | --- | --- | --- | --- |
    |Direto | preferredDataLocation | Escolha o atributo de origem | Desmarcada | Atualizar |

7. Para criar a regra de entrada, selecione **Adicionar**.

![Captura de tela de criar regra de sincronização de entrada](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-5-create-an-outbound-synchronization-rule"></a>Passo 5: Criar uma regra de sincronização de saída
A regra de sincronização de saída permite que o valor do atributo flua do metaverso para o atributo **preferredDataLocation** no Azure AD:

1. Vá para o **Editor de regras de sincronização**.
2. Defina a **direção** do filtro de pesquisa como de **saída**.
3. Selecione **Adicionar nova regra**.
4. Na guia **Descrição** , forneça a seguinte configuração:

    | Atributo | Value | Detalhes |
    | ----- | ------ | --- |
    | Name | *Forneça um nome* | Por exemplo, "out to Azure AD – User preferredDataLocation" |
    | Descrição | *Forneça uma descrição* ||
    | Sistema conectado | *Selecione o conector do AD do Azure* ||
    | Tipo de objeto do sistema conectado | **Usuário** ||
    | Tipo de objeto do metaverso | **Pessoa** ||
    | Tipo de Ligação | **Associar** ||
    | Precedência | *Escolha um número entre 1 e 99* | 1 a 99 é reservado para regras de sincronização personalizadas. Não escolha um valor que seja usado por outra regra de sincronização. |

5. Vá para a guia **filtro de escopo** e adicione um único grupo de filtro de escopo com duas cláusulas:

    | Atributo | Operator | Value |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Utilizador |
    | cloudMastered | NOTEQUAL | True |

    O filtro de escopo determina a quais objetos do Azure AD essa regra de sincronização de saída é aplicada. Neste exemplo, usamos o mesmo filtro de escopo da regra de sincronização OOB (fora de uso) do Azure AD – identidade do usuário. Ele impede que a regra de sincronização seja aplicada a objetos de **usuário** que não estão sincronizados do Active Directory local. Talvez seja necessário ajustar o filtro de escopo de acordo com sua implantação de Azure AD Connect.

6. Vá para a guia **transformação** e implemente a seguinte regra de transformação:

    | Tipo de fluxo | Atributo de destino | Source | Aplicar uma vez | Tipo de mesclagem |
    | --- | --- | --- | --- | --- |
    | Direto | preferredDataLocation | preferredDataLocation | Desmarcada | Atualizar |

7. Feche **Adicionar** para criar a regra de saída.

![Captura de tela de criar regra de sincronização de saída](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-6-run-full-synchronization-cycle"></a>Passo 6: Executar ciclo de sincronização completo
Em geral, o ciclo de sincronização completo é necessário. Isso ocorre porque você adicionou novos atributos ao Active Directory e ao esquema do Azure AD Connector e introduziu regras de sincronização personalizadas. Verifique as alterações antes de exportá-las para o Azure AD. Você pode usar as etapas a seguir para verificar as alterações, enquanto executa manualmente as etapas que compõem um ciclo de sincronização completo.

1. Execute a **importação completa** no conector de Active Directory local:

   1. Vá para a guia **operações** no Synchronization Service Manager.
   2. Clique com o botão direito do mouse no **conector de Active Directory local**e selecione **executar**.
   3. Na caixa de diálogo, selecione **importação completa**e selecione **OK**.
   4. Aguarde a conclusão da operação.

      > [!NOTE]
      > Você pode ignorar a importação completa no conector de Active Directory local se o atributo de origem já estiver incluído na lista de atributos importados. Em outras palavras, você não precisa fazer nenhuma alteração durante a etapa 2, anteriormente neste artigo.

2. Execute a **importação completa** no Azure ad Connector:

   1. Clique com o botão direito do mouse no **Azure ad Connector**e selecione **executar**.
   2. Na caixa de diálogo, selecione **importação completa**e selecione **OK**.
   3. Aguarde a conclusão da operação.

3. Verifique se a regra de sincronização é alterada em um objeto de **usuário** existente.

   O atributo de origem do Active Directory local e **preferredDataLocation** do Azure AD foram importados em cada espaço do conector respectivo. Antes de prosseguir com a etapa de sincronização completa, faça uma visualização em um objeto de **usuário** existente no espaço do conector de Active Directory local. O objeto escolhido deve ter o atributo de origem preenchido. Uma visualização bem-sucedida com **preferredDataLocation** populada no metaverso é um bom indicador de que você configurou as regras de sincronização corretamente. Para obter informações sobre como fazer uma visualização, consulte [verificar a alteração](how-to-connect-sync-change-the-configuration.md#verify-the-change).

4. Execute a **sincronização completa** no conector de Active Directory local:

   1. Clique com o botão direito do mouse no **conector de Active Directory local**e selecione **executar**.
   2. Na caixa de diálogo, selecione **sincronização completa**e selecione **OK**.
   3. Aguarde a conclusão da operação.

5. Verificar exportações pendentes para o Azure AD:

   1. Clique com o botão direito do mouse no **conector do Azure ad**e selecione **Pesquisar espaço do conector**.
   2. Na caixa de diálogo **Pesquisar espaço conector** :

        a. Defina o **escopo** como **exportação pendente**.<br>
        b. Marque todas as três caixas de seleção, incluindo **Adicionar, modificar e excluir**.<br>
        c. Para exibir a lista de objetos com as alterações a serem exportadas, selecione **Pesquisar**. Para examinar as alterações de um determinado objeto, clique duas vezes no objeto.<br>
        d. Verifique se as alterações são esperadas.

6. Executar **exportação** no **conector do AD do Azure**

   1. Clique com o botão direito do mouse no **Azure ad Connector**e selecione **executar**.
   2. Na caixa de diálogo **executar conector** , selecione **Exportar**e selecione **OK**.
   3. Aguarde a conclusão da operação.

> [!NOTE]
> Você pode observar que as etapas não incluem a etapa de sincronização completa no conector do Azure AD ou a etapa de exportação no conector de Active Directory. As etapas não são necessárias, pois os valores de atributo estão fluindo do Active Directory local para o Azure AD apenas.

## <a name="step-7-re-enable-sync-scheduler"></a>Passo 7: Reabilitar o Agendador de sincronização
Reabilitar o Agendador de sincronização interno:

1. Inicie uma sessão do PowerShell.
2. Habilite novamente a sincronização agendada executando este cmdlet:`Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-8-verify-the-result"></a>Passo 8: Verificar o resultado
Agora é hora de verificar a configuração e habilitá-la para seus usuários.

1. Adicione a área geográfica ao atributo selecionado em um usuário. A lista de áreas geográficas disponíveis pode ser encontrada nesta tabela.  
![Captura de tela do atributo do AD adicionado a um usuário](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Aguarde até que o atributo seja sincronizado com o Azure AD.
3. Usando o PowerShell do Exchange Online, verifique se a região da caixa de correio foi definida corretamente.  
![Captura de tela do PowerShell do Exchange Online](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Supondo que seu locatário foi marcado para ser capaz de usar esse recurso, a caixa de correio é movida para a área geográfica correta. Isso pode ser verificado examinando o nome do servidor onde a caixa de correio está localizada.
4. Para verificar se essa configuração foi eficaz em várias caixas de correio, use o script na [Galeria do TechNet](https://gallery.technet.microsoft.com/office/PowerShell-Script-to-a6bbfc2e). Esse script também tem uma lista dos prefixos de servidor de todos os datacenters do Office 365 e em qual área geográfica ele está localizado. Ele pode ser usado como uma referência na etapa anterior para verificar o local da caixa de correio.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre várias regiões geográficas no Office 365:

* [Sessões de várias regiões geográficas em Ignite](https://aka.ms/MultiGeoIgnite)
* [Várias regiões geográficas no OneDrive](https://aka.ms/OneDriveMultiGeo)
* [Várias regiões geográficas no SharePoint Online](https://aka.ms/SharePointMultiGeo)

Saiba mais sobre o modelo de configuração no mecanismo de sincronização:

* Leia mais sobre o modelo de configuração em [noções básicas sobre provisionamento](concept-azure-ad-connect-sync-declarative-provisioning.md)declarativo.
* Leia mais sobre a linguagem de expressão em [noções básicas sobre expressões de provisionamento](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)declarativo.

Tópicos de visão geral:

* [Sincronização do Azure AD Connect: Entender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
