---
title: 'Azure AD Connect: Configure localização de dados preferenciais para os recursos do Office 365'
description: Descreve como colocar os recursos de utilizador do Office 365 perto do utilizador com o sincronizado Azure Ative Directory Connect.
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
ms.date: 11/11/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 723411191d0990583d039a0fc9651437480807b4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80983267"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Sincronização de ligação de diretório ativo Azure: Configure localização de dados preferenciais para os recursos do Office 365
O objetivo deste tópico é acompanhá-lo sobre como configurar o atributo para localização de dados preferido no Azure Ative Directory (Azure AD) Connect sync. Quando alguém utiliza capacidades Multi-Geo no Office 365, utiliza-se este atributo para designar a geolocalização dos dados do Office 365 do utilizador. (Os termos *região* e *geo* são utilizados intercambiavelmente.)

## <a name="enable-synchronization-of-preferred-data-location"></a>Ativar a sincronização da localização de dados preferenciais
Por padrão, os recursos do Office 365 para os seus utilizadores estão localizados no mesmo geo que o seu inquilino Azure AD. Por exemplo, se o seu inquilino está localizado na América do Norte, então as caixas de correio de troca dos utilizadores também estão localizadas na América do Norte. Para uma organização multinacional, isto pode não ser o ideal.

Ao definir o atributo **preferidoDataLocation,** pode definir o geo de um utilizador. Pode ter os recursos do Office 365 do utilizador, como a caixa de correio e o OneDrive, no mesmo geo que o utilizador, e ainda ter um inquilino para toda a sua organização.

> [!IMPORTANT]
> A Multi-Geo está atualmente disponível para clientes com um Acordo Empresarial ativo e um mínimo de 500 assinaturas de Serviços do Office 365. Por favor, fale com o seu representante da Microsoft para obter mais detalhes.
>
>

Uma lista de todos os geos do Office 365 pode ser encontrada em [Onde estão os seus dados localizados?](https://aka.ms/datamaps)

Os geos do Office 365 disponíveis para multi-Geo são:

| Área Geográfica | valor preferidoDataLocalização |
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
| Estados Unidos | NAM |

* Se um geo não está listado nesta tabela (por exemplo, América do Sul), então não pode ser usado para Multi-Geo.

* Nem todas as cargas de trabalho do Office 365 suportam a utilização da definição do geo de um utilizador.

### <a name="azure-ad-connect-support-for-synchronization"></a>Suporte Azure AD Connect para sincronização

O Azure AD Connect suporta a sincronização do atributo **preferido dataLocation** para objetos **do Utilizador** na versão 1.1.524.0 e posteriormente. Mais concretamente:

* O esquema do tipo de objeto **Utilizador** no Conector AD Azure é estendido para incluir o atributo **preferido DataLocation.** O atributo é do tipo, corda de valor único.
* O esquema do tipo de objeto **Pessoa** no metaverso é estendido para incluir o atributo **preferido DataLocation.** O atributo é do tipo, corda de valor único.

Por predefinição, o **PreferredDataLocation** não está ativado para sincronização. Esta funcionalidade destina-se a organizações maiores. O esquema de Diretório Ativo no Windows Server 2019 tem um atributo **msDS preferidoDataLocation** que deve utilizar para este fim. Se não atualizou o esquema do Diretório Ativo e não o pode fazer, então deve identificar um atributo para manter o Artigo 365 geo do Office 365 para os seus utilizadores. Isto vai ser diferente para cada organização.

> [!IMPORTANT]
> O Azure AD permite que o atributo **preferido DataLocation** em **objetos** de utilizador em nuvem seja configurado diretamente utilizando o Azure AD PowerShell. Para configurar este atributo em **objetos de utilizador sincronizados,** tem de utilizar o Azure AD Connect.

Antes de permitir a sincronização:

* Se não atualizou o esquema do Diretório Ativo para 2019, então decida qual atributo do Diretório Ativo no local a utilizar como atributo de origem. Deve ser do tipo, **corda de valor único.**
* Se configurado previamente o atributo **preferido dataLocation** em **objetos de utilizador sincronizados** existentes em Azure AD utilizando o Azure AD PowerShell, deve retropor os valores de atributo si mesmos com os objetos de **utilizador** correspondentes no Diretório Ativo no local.

    > [!IMPORTANT]
    > Se não reverter estes valores, o Azure AD Connect remove os valores de atributo existentes em AD Azure quando a sincronização para o atributo **preferido dataLocation** está ativada.

* Configure o atributo de origem em pelo menos alguns objetos de utilizador de diretório ativo no local agora. Pode usá-lo para verificação mais tarde.

As seguintes secções fornecem os passos para permitir a sincronização do atributo **preferido DataLocation.**

> [!NOTE]
> Os passos são descritos no contexto de uma implantação da AD Azure com topologia de floresta única, e sem regras de sincronização personalizadas. Se tiver uma topologia multi-floresta, regras de sincronização personalizadas configuradas ou tiver um servidor de encenação, deve ajustar os passos em conformidade.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Passo 1: Desativar o programador de sincronização e verificar se não há sincronização em curso
Para evitar que alterações não intencionais sejam exportadas para a AD Azure, certifique-se de que não ocorre sincronização enquanto estiver no meio da atualização das regras de sincronização. Para desativar o programador de sincronização incorporado:

1. Inicie uma sessão powerShell no servidor Azure AD Connect.
2. Desative a sincronização programada executando este cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $false`.
3. Inicie o Gestor de Serviços de **Sincronização** indo para o **START** > **Synchronization Service**.
4. Selecione o separador **Operações** e confirme que não existe nenhuma operação com o estado *em curso*.

![Screenshot do Gestor de Serviços de Sincronização](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-refresh-the-schema-for-active-directory"></a>Passo 2: Refrescar o esquema para diretório ativo
Se atualizou o esquema do Diretório Ativo para 2019 e o Connect foi instalado antes da extensão do esquema, então a cache de esquema sintetização Connect não tem o esquema atualizado. Em seguida, deve refrescar o esquema do assistente para que apareça na UI.

1. Ligue o assistente Azure AD Connect a partir do ambiente de trabalho.
2. Selecione a opção **Refresh diretório schema** e clique **em Next**.
3. Introduza as suas credenciais De D AD Azure e clique **em Next**.
4. Na página **Refresh Directory Schema,** certifique-se de que todas as florestas são selecionadas e clique em **Next**.
5. Quando estiver concluído, feche o assistente.

![Screenshot de Refresh Directory Schema no assistente connect](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-refreshschema.png)

## <a name="step-3-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>Passo 3: Adicione o atributo de origem ao esquema de conector de diretório ativo no local
**Este passo só é necessário se executar a versão Connect 1.3.21 ou mais velha. Se estiver no 1.4.18 ou mais novo, salte para o passo 5.**  
Nem todos os atributos da AD Azure são importados para o espaço de conector Ative Directory no local. Se selecionou para utilizar um atributo que não é sincronizado por defeito, então precisa importá-lo. Para adicionar o atributo de origem à lista dos atributos importados:

1. Selecione o separador **Conectores** no Gestor de Serviçode Sincronização.
2. Clique no conector de diretório ativo no local e selecione **Propriedades**.
3. Na caixa de diálogo pop-up, aceda ao separador **Select Atributos.**
4. Certifique-se de que o atributo de origem selecionado para utilizar está verificado na lista de atributos. Se não vir o seu atributo, selecione a caixa de verificação **Show All.**
5. Para guardar, selecione **OK**.

![Screenshot do Gestor de Serviçode Sincronização e caixa de diálogo Properties](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-4-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Passo 4: Adicionar **localização de DataLocalização preferida** ao esquema do Conector AD Azure
**Este passo só é necessário se executar a versão Connect 1.3.21 ou mais velha. Se estiver no 1.4.18 ou mais novo, salte para o passo 5.**  
Por predefinição, o atributo **preferido dataLocation** não é importado para o espaço do Conector AD Azure. Para adicioná-lo à lista de atributos importados:

1. Selecione o separador **Conectores** no Gestor de Serviçode Sincronização.
2. Clique no conector Azure AD e selecione **Propriedades**.
3. Na caixa de diálogo pop-up, aceda ao separador **Select Atributos.**
4. Selecione o atributo **preferido DataLocation** na lista.
5. Para guardar, selecione **OK**.

![Screenshot do Gestor de Serviçode Sincronização e caixa de diálogo Properties](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-5-create-an-inbound-synchronization-rule"></a>Passo 5: Criar uma regra de sincronização de entrada
A regra de sincronização de entrada permite que o valor do atributo flua do atributo de origem no diretório ativo no local ao metaverso.

1. Inicie o Editor de Regras de **Sincronização** indo para **start** > **Synchronization Rules Editor**.
2. Desloque o filtro de procura **Direção** para estar **a caminho**de entrada .
3. Para criar uma nova regra de entrada, selecione **Adicionar nova regra**.
4. Sob o separador **Descrição,** forneça a seguinte configuração:

    | Atributo | Valor | Detalhes |
    | --- | --- | --- |
    | Nome | *Forneça um nome* | Por exemplo, "In from AD – User preferredDataLocation" |
    | Descrição | *Fornecer uma descrição personalizada* |  |
    | Sistema Conectado | *Escolha o conector de diretório ativo no local* |  |
    | Tipo de objeto de sistema conectado | **Utilizador** |  |
    | Tipo de objeto metaverso | **Pessoa** |  |
    | Tipo de ligação | **Aderir** |  |
    | Precedência | *Escolha um número entre 1-99* | 1-99 está reservado para regras de sincronização personalizadas. Não escolha um valor que seja utilizado por outra regra de sincronização. |

5. Mantenha o **filtro de deteção** vazio, para incluir todos os objetos. Pode ser necessário ajustar o filtro de deteção de acordo com a sua implementação Azure AD Connect.
6. Vá ao **separador Transformação**e implemente a seguinte regra de transformação:

    | Tipo de fluxo | Atributo-alvo | Origem | Aplicar uma vez | Tipo de fusão |
    | --- | --- | --- | --- | --- |
    |Direct | preferredDataLocation | Escolha o atributo de origem | Desselecionado | Atualizar |

7. Para criar a regra de entrada, selecione **Adicionar**.

![Screenshot da regra de sincronização de entrada](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-6-create-an-outbound-synchronization-rule"></a>Passo 6: Criar uma regra de sincronização de saída
A regra de sincronização de saída permite que o valor do atributo flua do metaverso para o atributo **preferido DataLocation** em Azure AD:

1. Vá ao Editor de Regras de **Sincronização.**
2. Desloque o filtro de procura **Direction** a **outbound**.
3. **Selecione Adicionar nova regra**.
4. Sob o separador **Descrição,** forneça a seguinte configuração:

    | Atributo | Valor | Detalhes |
    | ----- | ------ | --- |
    | Nome | *Forneça um nome* | Por exemplo, "out to Azure AD – User preferredDataLocation" |
    | Descrição | *Fornecer uma descrição* ||
    | Sistema Conectado | *Selecione o Conector Azure AD* ||
    | Tipo de objeto de sistema conectado | **Utilizador** ||
    | Tipo de objeto metaverso | **Pessoa** ||
    | Tipo de ligação | **Aderir** ||
    | Precedência | *Escolha um número entre 1-99* | 1-99 está reservado para regras de sincronização personalizadas. Não escolha um valor que seja utilizado por outra regra de sincronização. |

5. Vá ao separador **de filtro Scoping** e adicione um único grupo de filtros de deteção com duas cláusulas:

    | Atributo | Operador | Valor |
    | --- | --- | --- |
    | fonteObjectType | IGUAL | Utilizador |
    | cloudMastered | NÃO IGUAL | Verdadeiro |

    O filtro de deteção determina a que objetos Da AD Azure é aplicado a esta regra de sincronização de saída. Neste exemplo, utilizamos o mesmo filtro de digitalização de "out to Azure AD – User Identity" OOB (fora da caixa) regra de sincronização. Impede que a regra de sincronização seja aplicada a objetos **do Utilizador** que não sejam sincronizados a partir de um Diretório Ativo no local. Pode ser necessário ajustar o filtro de deteção de acordo com a sua implementação Azure AD Connect.

6. Vá ao separador **Transformação** e implemente a seguinte regra de transformação:

    | Tipo de fluxo | Atributo-alvo | Origem | Aplicar uma vez | Tipo de fusão |
    | --- | --- | --- | --- | --- |
    | Direct | preferredDataLocation | preferredDataLocation | Desselecionado | Atualizar |

7. Fechar **Adicionar** para criar a regra de saída.

![Screenshot da regra de sincronização de saída Create](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-7-run-full-synchronization-cycle"></a>Passo 7: Executar ciclo de sincronização completa
Em geral, é necessário um ciclo de sincronização total. Isto porque adicionou novos atributos ao esquema ative diretório e azure AD Connector, e introduziu regras de sincronização personalizadas. Verifique as alterações antes de exportá-las para a AD Azure. Pode utilizar os seguintes passos para verificar as alterações, enquanto executa manualmente os passos que compõem um ciclo de sincronização completa.

1. Executar **a importação total** no conector de diretório ativo no local:

   1. Vá ao separador **Operações** no Gestor de Serviços de Sincronização.
   2. Clique no **conector de diretório ativo no local**e selecione **Executar**.
   3. Na caixa de diálogo, selecione **Import Completo**, e selecione **OK**.
   4. Aguarde a operação para terminar.

      > [!NOTE]
      > Pode ignorar a importação total no conector de diretório ativo no local se o atributo de origem já estiver incluído na lista de atributos importados. Por outras palavras, não teve de fazer qualquer alteração durante o passo 2 anterior neste artigo.

2. Executar **a importação total** no Conector Azure AD:

   1. Clique à direita no **Conector AD Azure**e selecione **Executar**.
   2. Na caixa de diálogo, selecione **Import Completo**, e selecione **OK**.
   3. Aguarde a operação para terminar.

3. Verifique se a regra de sincronização muda num objeto **utilizador** existente.

   O atributo de origem do Diretório Ativo no local, e **a localização preferida dataLocation** da Azure AD, foram importados para cada espaço conector respetivo. Antes de prosseguir com a etapa de sincronização completa, faça uma pré-visualização de um objeto **utilizador** existente no espaço de conector de diretório ativo no local. O objeto que escolheste deve ter o atributo de origem povoado. Uma pré-visualização bem sucedida com **a PreferredDataLocation** povoada no metaverso é um bom indicador de que configura corretamente as regras de sincronização. Para obter informações sobre como fazer uma pré-visualização, consulte [Verificar a alteração](how-to-connect-sync-change-the-configuration.md#verify-the-change).

4. Executar **sincronização completa** no conector de diretório ativo no local:

   1. Clique no **conector de diretório ativo no local**e selecione **Executar**.
   2. Na caixa de diálogo, selecione **Full Synchronization**, e selecione **OK**.
   3. Aguarde a operação para terminar.

5. Verificar **as exportações pendentes** para a AD Azure:

   1. Clique no **Conector Azure AD**e selecione **Search Connector Space**.
   2. Na caixa de diálogo espaço do **conector de pesquisa:**

        a. Definir **o âmbito** para a **exportação pendente.**<br>
        b. Selecione as três caixas de verificação, incluindo **Adicionar, Modificar e Eliminar**.<br>
        c. Para ver a lista de objetos com alterações a exportar, selecione **Search**. Para examinar as alterações para um determinado objeto, clique duas vezes no objeto.<br>
        d. Verifique se as alterações são esperadas.

6. Executar **exportação** no **Conector Azure AD**

   1. Clique à direita no **Conector AD Azure**e selecione **Executar**.
   2. Na caixa de diálogo **Do Conector executar,** selecione **Export ,** e selecione **OK**.
   3. Aguarde a operação para terminar.

> [!NOTE]
> Pode notar que os passos não incluem o passo completo de sincronização no Conector AD Azure, ou o passo de exportação no Conector de Diretório Ativo. Os passos não são necessários, porque os valores do atributo estão fluindo do diretório ativo no local apenas para AD Azure.

## <a name="step-8-re-enable-sync-scheduler"></a>Passo 8: Reativar programador de sincronização
Reativar o programador de sincronização incorporado:

1. Inicie uma sessão powerShell.
2. Reativar a sincronização programada executando este cmdlet:`Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-9-verify-the-result"></a>Passo 9: Verificar o resultado
Chegou a hora de verificar a configuração e de a ativar para os seus utilizadores.

1. Adicione o geo ao atributo selecionado num utilizador. A lista de geos disponíveis pode ser encontrada nesta tabela.  
![Screenshot do atributo ad adicionado a um utilizador](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Aguarde que o atributo seja sincronizado com a AD Azure.
3. Utilizando o Exchange Online PowerShell, verifique se a região da caixa de correio foi corretamente definida.  
![Screenshot de Exchange Online PowerShell](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Assumindo que o seu inquilino foi marcado para poder usar esta funcionalidade, a caixa de correio é transferida para o geo correto. Isto pode ser verificado olhando para o nome do servidor onde a caixa de correio está localizada.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre multi-Geo no Office 365:

* [Sessões multi-Geo na Ignite](https://aka.ms/MultiGeoIgnite)
* [Multi-Geo no OneDrive](https://aka.ms/OneDriveMultiGeo)
* [Multi-Geo no SharePoint Online](https://aka.ms/SharePointMultiGeo)

Saiba mais sobre o modelo de configuração no motor de sincronização:

* Leia mais sobre o modelo de configuração em [Understanding Declarative Provisioning](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Leia mais sobre a linguagem de expressão na compreensão das expressões de [disposição declarativas.](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)

Tópicos de visão geral:

* [Sincronização Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
