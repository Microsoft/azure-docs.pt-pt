---
title: 'Sincronização do Azure Active Directory Connect: Configurar a localização de dados preferencial para Multi-Geo de recursos no Office 365 | Documentos da Microsoft'
description: Descreve como colocar os recursos de utilizador do Office 365 próximo do utilizador com a sincronização do Azure Active Directory Connect.
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
ms.date: 07/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a7b9c8827979ac4135bcaf4dfeef7cd5de02b2d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58118447"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Sincronização do Azure Active Directory Connect: Configurar a localização de dados preferencial para os recursos do Office 365
O objetivo deste tópico é orientá-lo como configurar o atributo de localização de dados preferencial na sincronização do Azure Active Directory (Azure AD) Connect. Quando alguém o utilizar capacidades de Multi-Geo no Office 365, utilize este atributo para designar a localização geográfica dos dados do utilizador do Office 365. (Os termos *região* e *geo* são utilizados alternadamente.)

## <a name="enable-synchronization-of-preferred-data-location"></a>Ativar a sincronização de localização de dados preferencial
Por predefinição, os recursos do Office 365 para os seus utilizadores estão localizados na mesma geografia que o inquilino do Azure AD. Por exemplo, se o seu inquilino está localizado na América do Norte, em seguida, caixas de correio do Exchange dos utilizadores são também localizadas na América do Norte. Para uma organização multinacional, este pode não ser ideal.

Ao definir o atributo **preferredDataLocation**, pode definir geo de um utilizador. Pode ter do Office 365 recursos o utilizador, tais como a caixa de correio e o OneDrive, na mesma geografia que o usuário e ainda tem um inquilino para toda a organização.

> [!IMPORTANT]
> Multi-Geo está atualmente disponível para clientes com um mínimo de 2.500 subscrições de serviços do Office 365. Fale com seu representante da Microsoft para obter detalhes.
>
>

Uma lista de todas as geos para o Office 365 pode ser encontrada na [é onde os dados localizados?](https://aka.ms/datamaps).

As geos no Office 365 disponíveis para o Multi-Geo são:

| Geo | valor de preferredDataLocation |
| --- | --- |
| Ásia-Pacífico | APC |
| Austrália | AUS |
| Canadá | CAN |
| União Europeia | EUR |
| França | FRA |
| Índia | IND |
| Japão | JPN |
| Coreia | KOR |
| Reino Unido | GBR |
| Estados Unidos | NAM |

* Se uma geo não estiver listada nesta tabela (por exemplo, América do Sul), em seguida, ele não pode ser utilizado para Multi-Geo.

* Nem todas as cargas de trabalho do Office 365 suportam a utilização da configuração geográfica de um utilizador.

### <a name="azure-ad-connect-support-for-synchronization"></a>Suporte do Azure AD Connect para sincronização

Azure AD Connect suporta a sincronização do **preferredDataLocation** atributo **utilizador** objetos na versão 1.1.524.0 e mais tarde. Especificamente:

* O esquema do tipo de objeto **usuário** no conector do Azure AD é expandida para incluir o **preferredDataLocation** atributo. O atributo é do tipo, cadeia de valor único.
* O esquema do tipo de objeto **pessoa** no metaverso é expandido para incluir o **preferredDataLocation** atributo. O atributo é do tipo, cadeia de valor único.

Por predefinição, **preferredDataLocation** não está ativado para sincronização. Esta funcionalidade destina-se para as grandes organizações. Também tem de identificar um atributo para conter a geo do Office 365 para os seus utilizadores, porque não existe nenhuma **preferredDataLocation** atributo no Active Directory no local. Isso vai ser diferente para cada organização.

> [!IMPORTANT]
> O Azure AD permite a **preferredDataLocation** atributo **objetos de utilizador da cloud** para ser configurado diretamente com o Azure AD PowerShell. O Azure AD já não permite a **preferredDataLocation** atributo **sincronizados objetos de utilizador** para ser configurado diretamente com o Azure AD PowerShell. Para configurar esse atributo nos **sincronizados objetos de utilizador**, tem de utilizar o Azure AD Connect.

Antes de ativar a sincronização:

* Decida quais atributos de Active Directory no local para ser utilizado como o atributo de origem. Deve ser do tipo, **cadeia de valor único**. Nos passos que se seguem, um da **extensionAttributes** é utilizado.
* Se configurou anteriormente o **preferredDataLocation** atribut existente **sincronizados objetos de utilizador** no Azure AD com o Azure AD PowerShell, tem backport o atributo valores para o correspondente **utilizador** objetos no Active Directory no local.

    > [!IMPORTANT]
    > Se fizer não backport estes valores, o Azure AD Connect remove os valores de atributos existentes no Azure AD quando a sincronização para o **preferredDataLocation** atributo está ativado.

* Configure agora o atributo de origem em, pelo menos, dois objetos de utilizador do Active Directory no local. Pode utilizá-lo para verificação posterior.

As secções seguintes fornecem os passos para ativar a sincronização do **preferredDataLocation** atributo.

> [!NOTE]
> As etapas são descritas no contexto de uma implementação do Azure AD com a topologia de floresta única e sem as regras de sincronização personalizado. Se tiver uma topologia de várias floresta, as regras de sincronização personalizado configurado ou tem um servidor de teste, deve ajustar as etapas apropriadamente.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Passo 1: Desativar o agendador de sincronização e certifique-se de que não existe nenhuma sincronização em curso
Para evitar alterações indesejadas a ser exportadas para o Azure AD, certifique-se de que não ocorre sincronização ocorre enquanto estiver no meio de a atualizar as regras de sincronização. Para desativar o agendador de sincronização interna:

1. Inicie uma sessão do PowerShell no servidor do Azure AD Connect.
2. Desativar a sincronização agendada ao executar este cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $false`.
3. Iniciar o **Synchronization Service Manager** indo até **iniciar** > **serviço de sincronização**.
4. Selecione o **Operations** separador e confirme que não existe nenhuma operação com o status *em curso*.

![Captura de ecrã do Gestor de serviço de sincronização](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>Passo 2: Adicione o atributo de origem para o esquema do conector do Active Directory no local
Nem todos os atributos do Azure AD são importados para o espaço de conector do Active Directory no local. Se tiver selecionado para usar um atributo que não está sincronizado por predefinição, em seguida, terá de importá-lo. Para adicionar o atributo de origem para a lista dos atributos importados:

1. Selecione o **conectores** separador o Synchronization Service Manager.
2. O conector do Active Directory no local com o botão direito e selecione **propriedades**.
3. Na caixa de diálogo pop-up, vá para o **selecionar atributos** separador.
4. Certifique-se de que selecionou para utilizar o atributo de origem está selecionado na lista de atributos. Se não vir o atributo, selecione o **Mostrar tudo** caixa de verificação.
5. Para guardar, selecione **OK**.

![Caixa de diálogo de propriedades e de captura de ecrã do Synchronization Service Manager](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Passo 3: Adicione **preferredDataLocation** ao esquema do conector do Azure AD
Por predefinição, o **preferredDataLocation** atributo não é importado para o espaço conector do Azure AD. Para adicioná-lo à lista de atributos importados:

1. Selecione o **conectores** separador o Synchronization Service Manager.
2. O conector do Azure AD com o botão direito e selecione **propriedades**.
3. Na caixa de diálogo pop-up, vá para o **selecionar atributos** separador.
4. Selecione o **preferredDataLocation** atributo na lista.
5. Para guardar, selecione **OK**.

![Caixa de diálogo de propriedades e de captura de ecrã do Synchronization Service Manager](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-4-create-an-inbound-synchronization-rule"></a>Passo 4: Criar uma regra de sincronização de entrada
A regra de sincronização de entrada que permite que o valor do atributo para o fluxo de atributo de origem no Active Directory no local para o metaverso.

1. Iniciar o **Editor de regras de sincronização** indo até **iniciar** > **Editor de regras de sincronização**.
2. Definir o filtro de pesquisa **direção** ser **entrada**.
3. Para criar uma nova regra de entrada, selecione **Adicionar nova regra**.
4. Sob o **Descrição** separador, forneça a seguinte configuração:

    | Atributo | Value | Detalhes |
    | --- | --- | --- |
    | Name | *Forneça um nome* | Por exemplo, "do AD – utilizador preferredDataLocation" |
    | Descrição | *Forneça uma descrição personalizada* |  |
    | Sistema ligado | *Escolha o conector do Active Directory no local* |  |
    | Tipo de objeto de sistema ligado | **Utilizador** |  |
    | Tipo de objeto de Metaverso | **Pessoa** |  |
    | Tipo de Ligação | **Associar** |  |
    | Precedência | *Escolha um número entre 1 a 99* | 1 a 99 está reservado para regras de sincronização personalizados. Não escolha um valor que é utilizado por outra regra de sincronização. |

5. Manter o **Scoping filtro** vazio, para incluir todos os objetos. Poderá ter de otimizar o filtro de âmbito, de acordo com a implementação do Azure AD Connect.
6. Vá para o **separador de transformação**e implementar a seguinte regra de transformação:

    | Tipo de fluxo | Atributo de destino | Origem | Aplicar uma vez | Tipo de intercalação |
    | --- | --- | --- | --- | --- |
    |Direto | preferredDataLocation | Escolha o atributo de origem | Desmarcada | Atualizar |

7. Para criar a regra de entrada, selecione **adicionar**.

![Captura de ecrã de criar a regra de sincronização de entrada](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-5-create-an-outbound-synchronization-rule"></a>Passo 5: Criar uma regra de sincronização de saída
A regra de sincronização de saída permite que o valor do atributo para o fluxo do metaverse para o **preferredDataLocation** atributo no Azure AD:

1. Vá para o **Editor de regras de sincronização**.
2. Definir o filtro de pesquisa **direção** ser **saída**.
3. Selecione **Adicionar nova regra**.
4. Sob o **Descrição** separador, forneça a seguinte configuração:

    | Atributo | Value | Detalhes |
    | ----- | ------ | --- |
    | Name | *Forneça um nome* | Por exemplo, "fora para o Azure AD – utilizador preferredDataLocation" |
    | Descrição | *Forneça uma descrição* ||
    | Sistema ligado | *Selecione o conector Azure AD* ||
    | Tipo de objeto de sistema ligado | **Utilizador** ||
    | Tipo de objeto de Metaverso | **Pessoa** ||
    | Tipo de Ligação | **Associar** ||
    | Precedência | *Escolha um número entre 1 a 99* | 1 a 99 está reservado para regras de sincronização personalizados. Não escolha um valor que é utilizado por outra regra de sincronização. |

5. Vá para o **Scoping filtro** separador e adicionar um único grupo de filtro de âmbito com dois cláusulas:

    | Atributo | Operador | Value |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Utilizador |
    | cloudMastered | NOTEQUAL | Verdadeiro |

    Filtro de âmbito determina que esta regra de sincronização de saída é aplicada para os objetos do Azure AD. Neste exemplo, usamos o mesmo filtro de âmbito de saída"para o AD – a identidade do usuário" regra de sincronização de OOB (out-of-box). Impede que a regra de sincronização a ser aplicada às **utilizador** objetos que não estão sincronizados do Active Directory no local. Poderá ter de otimizar o filtro de âmbito, de acordo com a implementação do Azure AD Connect.

6. Vá para o **transformação** separador e implementar a seguinte regra de transformação:

    | Tipo de fluxo | Atributo de destino | Origem | Aplicar uma vez | Tipo de intercalação |
    | --- | --- | --- | --- | --- |
    | Direto | preferredDataLocation | preferredDataLocation | Desmarcada | Atualizar |

7. Fechar **adicionar** para criar a regra de saída.

![Captura de ecrã de criar a regra de sincronização de saída](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-6-run-full-synchronization-cycle"></a>Passo 6: Execute o ciclo de sincronização completa
Em geral, o ciclo completo de sincronização é necessário. Isto acontece porque o tiver adicionado novos atributos para o Active Directory e o esquema do conector do Azure AD e introduziu as regras de sincronização personalizado. Verifique se as alterações antes de exportá-los para o Azure AD. Pode utilizar os seguintes passos para verificar as alterações, ao executar manualmente as etapas que formam um ciclo de sincronização completa.

1. Execute **importação completa** sobre o conector do Active Directory no local:

   1. Vá para o **operações** separador o Synchronization Service Manager.
   2. Com o botão direito a **conector do Active Directory no local**e selecione **executar**.
   3. Na caixa de diálogo, selecione **importação completa**e selecione **OK**.
   4. Aguarde pela conclusão da operação.

      > [!NOTE]
      > Pode ignorar a importação completa no conector do Active Directory no local se o atributo de origem já está incluído na lista de atributos importados. Em outras palavras, não era necessário que fazer qualquer alteração durante o passo 2 anteriormente no artigo.

2. Execute **importação completa** no conector do Azure AD:

   1. Com o botão direito a **conector do Azure AD**e selecione **executar**.
   2. Na caixa de diálogo, selecione **importação completa**e selecione **OK**.
   3. Aguarde pela conclusão da operação.

3. Verificar as alterações de regra de sincronização no existente **utilizador** objeto.

   O atributo de origem do Active Directory no local, e **preferredDataLocation** do Azure AD, tiverem sido importados para cada espaço conector respectivos. Antes de continuar com o passo de sincronização completa, fazer uma pré-visualização num existente **utilizador** objeto no espaço conector do Active Directory no local. O objeto que selecionou deve ter o atributo de origem preenchido. Uma bem-sucedida na pré-visualização com **preferredDataLocation** preenchido no metaverso é um bom indicador de que configurou a sincronização de regras corretamente. Para obter informações sobre como fazer uma pré-visualização, veja [verificar a alteração](how-to-connect-sync-change-the-configuration.md#verify-the-change).

4. Execute **completa sincronização** no conector do Active Directory no local:

   1. Com o botão direito a **conector do Active Directory no local**e selecione **executar**.
   2. Na caixa de diálogo, selecione **sincronização completa**e selecione **OK**.
   3. Aguarde pela conclusão da operação.

5. Certifique-se **exportações pendentes** para o Azure AD:

   1. Com o botão direito a **conector do Azure AD**e selecione **procurar espaço conector**.
   2. Na **procurar espaço conector** caixa de diálogo:

        a. Definir **âmbito** ao **pendentes exportação**.<br>
        b. Selecione todas as três caixas de verificação, incluindo **adicionar, modificar e eliminar**.<br>
        c. Para ver a lista de objetos com alterações seja exportada, selecione **pesquisa**. Para examinar as alterações para um determinado objeto, faça duplo clique o objeto.<br>
        d. Certifique-se de que as alterações esperadas.

6. Execute **exportar** sobre o **conector Azure AD**

   1. Com o botão direito a **conector do Azure AD**e selecione **executar**.
   2. Na **executar o conector** caixa de diálogo, selecione **exportar**e selecione **OK**.
   3. Aguarde pela conclusão da operação.

> [!NOTE]
> Pode observar que os passos não incluem o passo de sincronização completa no conector do Azure AD ou o passo de exportação no conector do Active Directory. Os passos não são necessários, porque os valores de atributos estão a fluir do Active Directory no local para o Azure AD apenas.

## <a name="step-7-re-enable-sync-scheduler"></a>Passo 7: Volte a ativar o agendador de sincronização
Volte a ativar o agendador de sincronização interna:

1. Inicie uma sessão do PowerShell.
2. Volte a Ativar sincronização agendada ao executar este cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-8-verify-the-result"></a>Passo 8: Verificar o resultado
Agora é hora de verificar a configuração e ativá-la para os seus utilizadores.

1. Adicione a geo para o atributo selecionado num utilizador. A lista de áreas geográficas disponíveis pode ser encontrada nesta tabela.  
![Captura de ecrã do atributo de AD adicionado a um utilizador](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Aguarde que o atributo a serem sincronizados para o Azure AD.
3. Utilizar o PowerShell do Exchange Online, certifique-se de que a região de caixa de correio foi definida corretamente.  
![Captura de ecrã do PowerShell do Exchange Online](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Partindo do princípio de que o inquilino foi marcado para poder utilizar esta funcionalidade, a caixa de correio é movida para a geo correta. Isto pode ser verificado ao observar o nome do servidor onde está localizada a caixa de correio.
4. Para verificar se esta definição se foram efetiva ao longo de várias caixas de correio, utilize o script na [galeria do TechNet](https://gallery.technet.microsoft.com/office/PowerShell-Script-to-a6bbfc2e). Este script também tem uma lista dos prefixos de servidor de todos os centros de dados do Office 365 e qual geo está localizado no. Ele pode ser usado como uma referência no passo anterior para verificar a localização da caixa de correio.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o Multi-Geo no Office 365:

* [Sessões de multi-Geo no Ignite](https://aka.ms/MultiGeoIgnite)
* [Multi-Geo no OneDrive](https://aka.ms/OneDriveMultiGeo)
* [Multi-Geo no SharePoint Online](https://aka.ms/SharePointMultiGeo)

Saiba mais sobre o modelo de configuração do motor de sincronização:

* Saiba mais sobre o modelo de configuração [aprovisionamento declarativo da compreensão](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Saiba mais sobre a linguagem de expressão no [Noções básicas sobre expressões de aprovisionamento declarativas](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

Tópicos de descrição geral:

* [Sincronização do Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
