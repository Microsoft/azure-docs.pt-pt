---
title: 'Azure AD Connect: Configurar a localização de dados preferenciais para os recursos da Microsoft 365'
description: Descreve como colocar os recursos do utilizador Microsoft 365 perto do utilizador com a sincronização do Azure Ative Directory Connect.
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
ms.topic: how-to
ms.date: 11/11/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ec5757b41da630c4cb09ad0c096aee87572615d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319899"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-microsoft-365-resources"></a>Azure Ative Directory Connect sync: Configurar a localização de dados preferenciais para os recursos da Microsoft 365
O objetivo deste tópico é interpor como configurar o atributo para localização de dados preferenciais no Azure Ative Directory (Azure AD) Connect sync. Quando alguém utiliza capacidades Multi-Geo no Microsoft 365, utiliza este atributo para designar a geolocalização dos dados microsoft 365 do utilizador. (Os termos *região* e *geo* são utilizados intercambiavelmente.)

## <a name="enable-synchronization-of-preferred-data-location"></a>Permitir a sincronização da localização de dados preferenciais
Por padrão, os recursos da Microsoft 365 para os seus utilizadores estão localizados no mesmo geo que o seu inquilino AZure AD. Por exemplo, se o seu inquilino estiver localizado na América do Norte, então as caixas de correio de troca dos utilizadores também estão localizadas na América do Norte. Para uma organização multinacional, isto pode não ser o ideal.

Ao definir o atributo **PreferredDataLocation,** pode definir o geo de um utilizador. Pode ter os recursos Microsoft 365 do utilizador, como a caixa de correio e o OneDrive, no mesmo geo que o utilizador, e ainda ter um inquilino para toda a sua organização.

> [!IMPORTANT]
> A Multi-Geo está atualmente disponível para clientes com um Acordo Empresarial ativo e um mínimo de 250 subscrições de Serviços Microsoft 365. Por favor, fale com o seu representante da Microsoft para mais detalhes.
>
>

Uma lista de todos os geos para o Microsoft 365 pode ser encontrada em [Onde estão os seus dados localizados?](https://aka.ms/datamaps)

Os geos da Microsoft 365 disponíveis para Multi-Geo são:

| Área Geográfica | valor preferencial DataLocation |
| --- | --- |
| Ásia-Pacífico | APC |
| Austrália | AUS |
| Canadá | PODE |
| União Europeia | EUR |
| França | FRA |
| Índia | IND |
| Japão | JPN |
| Coreia | KOR |
| África do Sul | ZAF |
| Suíça | CHE |
| Emirados Árabes Unidos | SÃO |
| Reino Unido | GBR |
| Estados Unidos da América | NAM |

* Se um geo não estiver listado nesta tabela (por exemplo, América do Sul), então não pode ser utilizado para multi-geo.

* Nem todas as cargas de trabalho da Microsoft 365 suportam a utilização da definição do geo de um utilizador.

### <a name="azure-ad-connect-support-for-synchronization"></a>Suporte AZURE AD Connect para sincronização

O Azure AD Connect suporta a sincronização do atributo **DataLocation preferido** para objetos **do Utilizador** na versão 1.1.524.0 e posterior. Especificamente:

* O esquema do tipo de objeto **Utilizador** no Conector AZURE AD é estendido para incluir o atributo **DataLocation preferido.** O atributo é do tipo, corda de valor único.
* O esquema do tipo de objeto **Pessoa** no metaverso é estendido para incluir o atributo **DataLocation preferido.** O atributo é do tipo, corda de valor único.

Por predefinição, **a PreferredDataLocation** não está ativada para sincronização. Esta funcionalidade destina-se a organizações maiores. O esquema de Diretório Ativo no Windows Server 2019 tem um atributo **de MsDS-preferredDataLocation** que deve utilizar para este fim. Se não atualizou o esquema do Ative Directory e não o pode fazer, então deve identificar um atributo para manter o microsoft 365 geo para os seus utilizadores. Isto vai ser diferente para cada organização.

> [!IMPORTANT]
> O Azure AD permite que o atributo **DataLocation preferido** nos **objetos do Utilizador** na nuvem seja configurado diretamente utilizando o Azure AD PowerShell. Para configurar este atributo em **objetos do Utilizador sincronizados,** tem de utilizar o Azure AD Connect.

Antes de permitir a sincronização:

* Se não atualizou o esquema do Ative Directory para 2019, então decida qual o atributo Ative Directory no local para ser usado como atributo de origem. Deve ser do tipo, **corda de valor único.**
* Se tiver configurado previamente o atributo **DataLocation preferido** nos **objetos do Utilizador sincronizados existentes** em AD AD utilizando o Azure AD PowerShell, deve utilizar os valores de atributos correspondentes aos objetos **do Utilizador** correspondentes no Ative Directory.

    > [!IMPORTANT]
    > Se não recuar nestes valores, o Azure AD Connect remove os valores de atributos existentes em AZure AD quando a sincronização para o **atributoDataLocation preferido** está ativada.

* Configure o atributo de origem em pelo menos um par de objetos de utilizador ativo no local agora. Pode usá-lo para verificação mais tarde.

As secções seguintes fornecem os passos para permitir a sincronização do atributo **DataLocation preferido.**

> [!NOTE]
> Os passos são descritos no contexto de uma implantação AZure AD com topologia de floresta única, e sem regras de sincronização personalizadas. Se tiver uma topologia multi-floresta, regras de sincronização personalizadas configuradas ou tiver um servidor de preparação, deve ajustar os passos em conformidade.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Passo 1: Desativar o programador de sincronização e verificar se não há sincronização em curso
Para evitar que sejam exportadas alterações não intencionais para a Azure AD, certifique-se de que não ocorre sincronização enquanto estiver a atualizar as regras de sincronização. Para desativar o programador de sincronização incorporado:

1. Inicie uma sessão PowerShell no servidor Azure AD Connect.
2. Desative a sincronização programada executando este cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $false` .
3. Inicie o **Gestor de Serviços de Sincronização** indo ao **Serviço de**  >  **Sincronização**START .
4. Selecione o separador **Operações** e confirme que não há nenhuma operação com o estado *em curso*.

![Screenshot do Gestor de Serviços de Sincronização](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-refresh-the-schema-for-active-directory"></a>Passo 2: Refrescar o esquema para o Ative Directory
Se atualizou o esquema do Ative Directory para 2019 e o Connect foi instalado antes da extensão do esquema, então a cache de esquemas Connect não tem o esquema atualizado. Em seguida, deve refrescar o esquema do assistente para que apareça na UI.

1. Inicie o assistente AZure AD Connect a partir do ambiente de trabalho.
2. Selecione a opção **Refresh directy schema** e clique em **Seguinte**.
3. Insira as suas credenciais AD AZure e clique em **Seguinte**.
4. Na página **Refresh Directory Schema,** certifique-se de que todas as florestas estão selecionadas e clique em **Seguinte**.
5. Quando estiver concluído, feche o assistente.

![Screenshot do Refresh Directory Schema no assistente Connect](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-refreshschema.png)

## <a name="step-3-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>Passo 3: Adicione o atributo de origem ao esquema de ligação de diretório ativo no local
**Este passo só é necessário se executar a versão 1.3.21 ou mais antiga do Connect. Se estiver em 1.4.18 ou mais novo, então salte para o passo 5.**  
Nem todos os atributos AD AZure são importados para o espaço de conector Ative Directory no local. Se selecionou para usar um atributo que não é sincronizado por padrão, então precisa importá-lo. Para adicionar o atributo de origem à lista dos atributos importados:

1. Selecione o **separador Conectores** no Gestor de Serviço de Sincronização.
2. Clique com o botão direito no conector ative do diretório e selecione **Propriedades**.
3. Na caixa de diálogo pop-up, aceda ao **separador 'Selecionar Atributos'.**
4. Certifique-se de que o atributo de origem selecionado para utilizar é verificado na lista de atributos. Se não vir o seu atributo, selecione a caixa de verificação **'Mostrar Tudo'.**
5. Para guardar, selecione **OK**.

![Screenshot que mostra a caixa de diálogo do Gestor de Serviço de Sincronização e propriedades com a lista de "Atributos" em destaque.](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-4-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Passo 4: **AdicionarDataLocation preferencial** ao esquema do conector AZure AD
**Este passo só é necessário se executar a versão 1.3.21 ou mais antiga do Connect. Se estiver em 1.4.18 ou mais novo, então salte para o passo 5.**  
Por predefinição, o atributo **DataLocation preferido** não é importado para o espaço Azure AD Connector. Para adicioná-lo à lista de atributos importados:

1. Selecione o **separador Conectores** no Gestor de Serviço de Sincronização.
2. Clique com o botão direito no conector AD Azure e selecione **Propriedades**.
3. Na caixa de diálogo pop-up, aceda ao **separador 'Selecionar Atributos'.**
4. Selecione o **atributoDataLocation preferido** na lista.
5. Para guardar, selecione **OK**.

![Screenshot do Gestor de Serviço de Sincronização e caixa de diálogo de propriedades](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-5-create-an-inbound-synchronization-rule"></a>Passo 5: Criar uma regra de sincronização de entrada
A regra de sincronização de entrada permite que o valor do atributo flua do atributo de origem no Diretório Ativo para o metaverso.

1. Inicie o **Editor de Regras de Sincronização** indo ao **START**  >  **Synchronization Rules Editor**.
2. Desa esta hora de **configurar** o filtro de pesquisa para a **entrada**.
3. Para criar uma nova regra de entrada, **selecione Adicione nova regra**.
4. No **separador Descrição,** forneça a seguinte configuração:

    | Atributo | Valor | Detalhes |
    | --- | --- | --- |
    | Nome | *Fornecer um nome* | Por exemplo, "In from AD – User preferredDataLocation" |
    | Descrição | *Fornecer uma descrição personalizada* |  |
    | Sistema Conectado | *Escolha o conector ative de diretório no local* |  |
    | Tipo de objeto de sistema conectado | **Utilizador** |  |
    | Tipo de objeto metaverso | **Pessoa** |  |
    | Tipo de ligação | **Join** |  |
    | Precedência | *Escolha um número entre 1 e 99* | 1-99 está reservado para regras de sincronização personalizadas. Não escolha um valor que seja utilizado por outra regra de sincronização. |

5. Mantenha o **filtro de escotagem** vazio, para incluir todos os objetos. Pode ser necessário ajustar o filtro de deteção de acordo com a sua implementação Azure AD Connect.
6. Vá ao **separador Transformação**e implemente a seguinte regra de transformação:

    | Tipo de fluxo | Atributo-alvo | Origem | Aplicar uma vez | Tipo de fusão |
    | --- | --- | --- | --- | --- |
    |Direct | PreferredDataLocation | Escolha o atributo de origem | Desselecionado | Atualizar |

7. Para criar a regra de entrada, **selecione Adicionar**.

![Screenshot da regra de sincronização de entrada criar](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-6-create-an-outbound-synchronization-rule"></a>Passo 6: Criar uma regra de sincronização de saída
A regra de sincronização de saída permite que o valor do atributo flua do metaverso para o **atributoDataLocation preferido** em Azure AD:

1. Vá ao **Editor de Regras de Sincronização.**
2. Desa esta hora o filtro de pesquisa **para** ser **de saída**.
3. **Selecione Adicionar nova regra**.
4. No **separador Descrição,** forneça a seguinte configuração:

    | Atributo | Valor | Detalhes |
    | ----- | ------ | --- |
    | Nome | *Fornecer um nome* | Por exemplo, "out to Azure AD – User preferredDataLocation" |
    | Descrição | *Fornecer uma descrição* ||
    | Sistema Conectado | *Selecione o Conector AD Azure* ||
    | Tipo de objeto de sistema conectado | **Utilizador** ||
    | Tipo de objeto metaverso | **Pessoa** ||
    | Tipo de ligação | **Join** ||
    | Precedência | *Escolha um número entre 1 e 99* | 1-99 está reservado para regras de sincronização personalizadas. Não escolha um valor que seja utilizado por outra regra de sincronização. |

5. Vá ao **separador filtro de scoping** e adicione um único grupo de filtro de digitalização com duas cláusulas:

    | Atributo | Operador | Valor |
    | --- | --- | --- |
    | sourceObjectType | IGUAL | Utilizador |
    | cloudMastered | NOTAQUAL | Verdadeiro |

    O filtro de deteção determina quais os objetos AZure AD a que esta regra de sincronização de saída é aplicada. Neste exemplo, utilizamos o mesmo filtro de deteção de "out to Azure AD – User Identity" OOB (fora da caixa) regra de sincronização. Impede que a regra de sincronização seja aplicada a objetos do **Utilizador** que não sejam sincronizados a partir de um Diretório Ativo no local. Pode ser necessário ajustar o filtro de deteção de acordo com a sua implementação Azure AD Connect.

6. Vá ao separador **Transformação** e implemente a seguinte regra de transformação:

    | Tipo de fluxo | Atributo-alvo | Origem | Aplicar uma vez | Tipo de fusão |
    | --- | --- | --- | --- | --- |
    | Direct | PreferredDataLocation | PreferredDataLocation | Desselecionado | Atualizar |

7. Fechar **Adicionar** para criar a regra de saída.

![Screenshot da regra de sincronização de saída criar](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-7-run-full-synchronization-cycle"></a>Passo 7: Executar ciclo de sincronização completa
Em geral, é necessário um ciclo completo de sincronização. Isto porque adicionou novos atributos ao esquema de Conexão Ative E Azure AD, e introduziu regras de sincronização personalizadas. Verifique as alterações antes de exportá-las para a Azure AD. Pode utilizar os seguintes passos para verificar as alterações, enquanto executa manualmente os passos que compõem um ciclo de sincronização completo.

1. Executar **a importação completa** no conector ative do Diretório Ativo:

   1. Aceda ao separador **Operações** no Gestor de Serviços de Sincronização.
   2. Clique com o botão direito **no conector ative do diretório,** e selecione **Executar**.
   3. Na caixa de diálogo, selecione **Full Import**, e selecione **OK**.
   4. Aguarde que a operação esteja concluída.

      > [!NOTE]
      > Pode ignorar a importação completa no Ative Directory Connector se o atributo de origem já estiver incluído na lista de atributos importados. Por outras palavras, não teve de fazer qualquer alteração durante o passo 2 anterior neste artigo.

2. Executar **a importação completa** no Conector AD Azure:

   1. Clique com o botão direito no **Conector AD Azure**e selecione **Executar**.
   2. Na caixa de diálogo, selecione **Full Import**, e selecione **OK**.
   3. Aguarde que a operação esteja concluída.

3. Verifique as alterações da regra de sincronização num objeto **do Utilizador** existente.

   O atributo de origem do Ative Directy no local, e **o DadoLocation preferido** da Azure AD, foram importados para cada espaço de conector respetivo. Antes de prosseguir com o passo de sincronização completo, faça uma pré-visualização de um objeto **do Utilizador** existente no espaço ative directory connector. O objeto que escolheu deve ter o atributo de origem povoado. Uma pré-visualização bem sucedida com o **FavoritoDataLocation** preenchido no metaverso é um bom indicador de que configuraste corretamente as regras de sincronização. Para obter informações sobre como fazer uma pré-visualização, consulte [Verificar a alteração](how-to-connect-sync-change-the-configuration.md#verify-the-change).

4. Executar **sincronização completa** no conector ative do diretório no local:

   1. Clique com o botão direito **no conector ative do diretório,** e selecione **Executar**.
   2. Na caixa de diálogo, selecione **a Sincronização Completa**e selecione **OK**.
   3. Aguarde que a operação esteja concluída.

5. Verifique **as exportações pendentes** para a Azure AD:

   1. Clique com o botão direito no **Conector AD Azure**e selecione **Search Connector Space**.
   2. Na caixa de diálogo **do espaço do conector de busca:**

        a. Definir **âmbito** de **exportação pendente**.<br>
        b. Selecione as três caixas de verificação, incluindo **Adicionar, Modificar e Eliminar**.<br>
        c. Para ver a lista de objetos com alterações a exportar, selecione **Pesquisar**. Para examinar as alterações para um determinado objeto, clique duas vezes no objeto.<br>
        d. Verifique se as alterações são esperadas.

6. **Executar exportação** no **conector AD Azure**

   1. Clique com o botão direito no **Conector AD Azure**e selecione **Executar**.
   2. Na caixa de diálogo **run connector,** selecione **Export**, e selecione **OK**.
   3. Aguarde que a operação esteja concluída.

> [!NOTE]
> Pode notar que os passos não incluem o passo completo de sincronização no Conector AD Azure ou o passo de exportação no Conector de Diretório Ativo. Os passos não são necessários, porque os valores do atributo estão a fluir do Ative Directory para o Azure apenas.

## <a name="step-8-re-enable-sync-scheduler"></a>Passo 8: Ativar o programador de sincronização
Re-activar o programador de sincronização incorporado:

1. Inicie uma sessão PowerShell.
2. Re-activar a sincronização programada executando este cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-9-verify-the-result"></a>Passo 9: Verificar o resultado
É agora altura de verificar a configuração e de a capacitar para os seus utilizadores.

1. Adicione o geo ao atributo selecionado num utilizador. A lista de geos disponíveis pode ser encontrada nesta tabela.  
![Screenshot do atributo AD adicionado a um utilizador](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Aguarde que o atributo seja sincronizado com a Azure AD.
3. Utilizando o Exchange Online PowerShell, verifique se a região da caixa de correio foi corretamente definida.  
![Screenshot de Exchange Online PowerShell](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Assumindo que o seu inquilino foi marcado para poder usar esta funcionalidade, a caixa de correio é transferida para o geo correto. Isto pode ser verificado olhando para o nome do servidor onde a caixa de correio está localizada.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o Multi-Geo na Microsoft 365:

* [Sessões multi-geo em Ignite](https://aka.ms/MultiGeoIgnite)
* [Multi-Geo em OneDrive](https://aka.ms/OneDriveMultiGeo)
* [Multi-Geo no SharePoint Online](https://aka.ms/SharePointMultiGeo)

Saiba mais sobre o modelo de configuração no motor de sincronização:

* Leia mais sobre o modelo de configuração na [Compreensão declarativa.](concept-azure-ad-connect-sync-declarative-provisioning.md)
* Leia mais sobre a linguagem de expressão em [Compreensão Declarativas Expressões.](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)

Tópicos de visão geral:

* [Azure AD Connect sync: Entenda e personalize a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
