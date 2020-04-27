---
title: Azure AD Connect - Erros BigObject causados pelo atributo do UserCertificate [ Microsoft Docs
description: Este tópico fornece os passos de reparação para erros de LargeObject causados pelo atributo do UserCertificate.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 146ad5b3-74d9-4a83-b9e8-0973a19828d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: c851b5ef024e6584e6f8c93995208b08a91fbb60
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "62095494"
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Sincronização azure AD Connect: Manusear erros bigObject causados pelo atributo do userCertificate

A Azure AD impõe um limite máximo de **15** valores de certificado no atributo do Certificado de **utilizador.** Se a Azure AD Connect exportar um objeto com mais de 15 valores para a AD Azure, a Azure AD devolve um erro **da LargeObject** com mensagem:

>*"O objeto provisionado é muito grande. Corte o número de valores de atributo neste objeto. A operação será novamente julgada no próximo ciclo de sincronização..."*

O erro Do LargeObject pode ser causado por outros atributos ad. Para confirmar que é de facto causado pelo atributo do UserCertificate, é necessário verificar contra o objeto no local ou no Sistema de Pesquisa Metaverse do Gestor de Serviços de [Sincronização.](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-mvsearch)

Para obter a lista de objetos no seu inquilino com erros Da LargeObject, utilize um dos seguintes métodos:

 * Se o seu inquilino estiver habilitado para a Azure AD Connect Health para sincronização, pode consultar o Relatório de Erro de [Sincronização](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-sync) fornecido.
 
 * O e-mail de notificação para erros de sincronização de diretório sincronia que é enviado no final de cada ciclo de sincronização tem a lista de objetos com erros Do BigObject. 
 * O [separador Operações](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-operations) de Sincronização mostra a lista de objetos com erros BigObject se clicar na mais recente operação Export to Azure AD.
 
## <a name="mitigation-options"></a>Opções de mitigação
Até que o erro do LargeObject seja resolvido, outras alterações de atributos para o mesmo objeto não podem ser exportadas para a AD Azure. Para resolver o erro, pode considerar as seguintes opções:

 * Atualize Azure AD Connect para construir 1.1.524.0 ou depois. No Azure AD Connect construir 1.1.524.0, as regras de sincronização fora da caixa foram atualizadas para não exportar atribuicertificado de utilizador e userSMIMECertificate se os atributos tiverem mais de 15 valores. Para mais detalhes sobre como atualizar o Azure AD Connect, consulte o artigo [Azure AD Connect: Upgrade de uma versão anterior para o mais recente](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

 * Implementar uma **regra de sincronização** de saída no Azure AD Connect que exporta um **valor nulo em vez dos valores reais para objetos com mais de 15 valores**de certificado . Esta opção é adequada se não exigir que nenhum dos valores do certificado seja exportado para a AD Azure para objetos com mais de 15 valores. Para obter mais informações sobre como implementar esta regra de sincronização, consulte a regra de execução da próxima secção [implementando para limitar a exportação do atributo do UserCertificate](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute).

 * Reduza o número de valores de certificado no objeto AD no local (15 ou menos) removendo valores que já não estão a ser utilizados pela sua organização. Isto é adequado se o inchaço do atributo for causado por certificados expirados ou não utilizados. Pode utilizar o [script PowerShell disponível aqui](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) para ajudar a encontrar, fazer backup e eliminar certificados expirados no seu Anúncio no local. Antes de apagar os certificados, recomenda-se que verifique com os administradores de infraestruturas públicas na sua organização.

 * Configure Azure AD Connect para excluir o atributo do Certificado de utilizador de ser exportado para a AD Azure. Em geral, não recomendamos esta opção, uma vez que o atributo pode ser utilizado pelos Serviços Online da Microsoft para permitir cenários específicos. Em particular:
    * O atributo userCertificate no objeto user é utilizado pelos clientes Exchange Online e Outlook para a assinatura e encriptação de mensagens. Para saber mais sobre esta funcionalidade, consulte o artigo [S/MIME para a assinatura e encriptação de mensagens.](https://technet.microsoft.com/library/dn626158(v=exchg.150).aspx)

    * O atributo do UserCertificate no objeto do Computador é utilizado pela Azure AD para permitir que dispositivos ligados ao domínio do Windows 10 no local se conectem ao Azure AD. Para saber mais sobre esta funcionalidade, consulte o artigo Connect dispositivos unidos pelo [domínio para a AD Azure para experiências do Windows 10](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy).

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>Regra de execução de sincronização para limitar exportação de atributo de certificado de utilizador
Para resolver o erro do LargeObject causado pelo atributo do UserCertificate, pode implementar uma regra de sincronização de saída no Azure AD Connect que exporta um **valor nulo em vez dos valores reais para objetos com mais de 15 valores de certificado**. Esta secção descreve os passos necessários para implementar a regra de sincronização para objetos **do Utilizador.** Os passos podem ser adaptados para **objetos de contacto** e **computador.**

> [!IMPORTANT]
> Exportar valor nulo remove valores de certificado anteriormente exportados com sucesso para a AD Azure.

Os passos podem ser resumidos como:

1. Desative o programador de sincronização e verifique se não há sincronização em curso.
3. Encontre a regra de sincronização de saída existente para o atributo do Certificado de utilizador.
4. Crie a regra de sincronização de saída necessária.
5. Verifique a nova regra de sincronização num objeto existente com erro LargeObject.
6. Aplique a nova regra de sincronização aos objetos restantes com erro Do LargeObject.
7. Verifique se não há alterações inesperadas à espera de serem exportadas para a AD Azure.
8. Exportar as alterações para AD Azure.
9. Reativar o programador de sincronização.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Passo 1. Desative o programador de sincronização e verifique se não há sincronização em curso
Certifique-se de que não ocorre sincronização enquanto estiver a implementar uma nova regra de sincronização para evitar que alterações não intencionais sejam exportadas para a AD Azure. Para desativar o programador de sincronização incorporado:
1. Inicie a sessão PowerShell no servidor Azure AD Connect.

2. Desativar a sincronização programada por cmdlet em execução:`Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> Os passos anteriores só se aplicam às versões mais recentes (1.1.xxx.x) do Azure AD Connect com o programador incorporado. Se estiver a utilizar versões mais antigas (1.0.xxx.x) do Azure AD Connect que utiliza o Programador de Tarefas do Windows, ou se estiver a utilizar o seu próprio programador personalizado (não é comum) para desencadear uma sincronização periódica, tem de os desativar em conformidade.

1. Inicie o Gestor de Serviços de **Sincronização** indo para o START → Serviço de Sincronização.

1. Vá ao separador **Operações** e confirme que não há nenhuma operação cujo estado esteja *"em curso".*

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>Passo 2. Encontre a regra de sincronização de saída existente para o atributo do UserCertificate
Deve existir uma regra de sincronização existente que esteja ativada e configurada para exportar o atributo do UserCertificate para objetos de utilizador para a AD Azure. Localize esta regra de sincronização para descobrir a sua **precedência** e a configuração do filtro de **deteção:**

1. Inicie o Editor de Regras de **Sincronização** indo para START → Sincronização De Regras Editor.

2. Configure os filtros de pesquisa com os seguintes valores:

    | Atributo | Valor |
    | --- | --- |
    | Direção |**Saída** |
    | Tipo de objeto MV |**Pessoa** |
    | Conector |*nome do seu conector Azure AD* |
    | Tipo de objeto de conector |**utilizador** |
    | Atributo MV |**userCertificate** |

3. Se estiver a utilizar regras de sincronização OOB (fora da caixa) para o conector Azure AD para exportar atributo do utilizadorCertficiate para objetos de utilizador, deverá voltar a receber a regra *"out to AAD – User ExchangeOnline".*
4. Note o valor de **precedência** desta regra de sincronização.
5. Selecione a regra de sincronização e clique em **Editar**.
6. No diálogo pop-up *"Editar Confirmação de Regra Reservada",* clique **em No**. (Não se preocupe, não vamos fazer nenhuma alteração a esta regra de sincronização).
7. No ecrã de edição, selecione o separador filtro **Scoping.**
8. Note a configuração do filtro de deteção. Se estiver a utilizar a regra de sincronização OOB, deve existir exatamente um grupo de **filtros de deteção contendo duas cláusulas**, incluindo:

    | Atributo | Operador | Valor |
    | --- | --- | --- |
    | fonteObjectType | IGUAL | Utilizador |
    | cloudMastered | NÃO IGUAL | Verdadeiro |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>Passo 3. Criar a regra de sincronização de saída necessária
A nova regra de sincronização deve ter o mesmo filtro de **deteção** e **precedência mais elevada** do que a regra de sincronização existente. Isto garante que a nova regra de sincronização se aplica ao mesmo conjunto de objetos que a regra de sincronização existente e substitui a regra de sincronização existente para o atributo do Certificado de utilizador. Para criar a regra de sincronização:
1. No Editor de Regras de Sincronização, clique no novo botão de **regra Adicionar.**
2. Sob o **separador Descrição,** forneça a seguinte configuração:

    | Atributo | Valor | Detalhes |
    | --- | --- | --- |
    | Nome | *Forneça um nome* | Por exemplo, *"out to AAD – Custom overover for userCertificate"* |
    | Descrição | *Fornecer uma descrição* | Por exemplo, "Se o atributo do userCertificate tiver mais de *15 valores, exporte NULL."* |
    | Sistema Conectado | *Selecione o Conector Azure AD* |
    | Tipo de objeto de sistema conectado | **utilizador** | |
    | Tipo de objeto metaverso | **pessoa** | |
    | Tipo de ligação | **Aderir** | |
    | Precedência | *Escolheu um número entre 1 - 99* | O número escolhido não deve ser utilizado por qualquer regra de sincronização existente e tem um valor mais baixo (e, portanto, maior precedência) do que a regra de sincronização existente. |

3. Vá ao separador de **filtro Scoping** e implemente o mesmo filtro de deteção que a regra de sincronização existente está a usar.
4. Ignore o separador de **regras de Ads.**
5. Vá ao separador **Transformações** para adicionar uma nova transformação usando a seguinte configuração:

    | Atributo | Valor |
    | --- | --- |
    | Tipo de Fluxo |**Expressão** |
    | Atributo-alvo |**userCertificate** |
    | Atributo fonte |*Utilize a seguinte expressão:*`IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. Clique no botão **Adicionar** para criar a regra de sincronização.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>Passo 4. Verifique a nova regra de sincronização de um objeto existente com erro LargeObject
Isto é para verificar se a regra de sincronização criada está a funcionar corretamente num objeto AD existente com erro LargeObject antes de o aplicar a outros objetos:
1. Vá ao separador **Operações** no Gestor de Serviços de Sincronização.
2. Selecione a mais recente operação Export to Azure AD e clique num dos objetos com erros BigObject.
3.  No ecrã pop-up Do Connector Space Object Properties, clique no botão **Preview.**
4. No ecrã pop-up de pré-visualização, selecione **a sincronização completa** e clique em **'Pré-visualização'.**
5. Feche o ecrã de pré-visualização e o ecrã de propriedades de objetos espaciais do conector.
6. Vá ao separador **Conectores** no Gestor de Serviçode Sincronização.
7. Clique à direita no Conector **Azure AD** e selecione **Executar...**
8. No pop-up run Connector, selecione **Passo de Exportação** e clique **OK**.
9. Aguarde que a Exportação para a AD Azure esteja concluída e confirme que não há mais nenhum erro da LargeObject neste objeto específico.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>Passo 5. Aplique a nova regra de sincronização aos objetos restantes com erro LargeObject
Uma vez adicionada a regra de sincronização, é necessário executar um passo de sincronização completo no Conector AD:
1. Vá ao separador **Conectores** no Gestor de Serviçode Sincronização.
2. Clique à direita no Conector **AD** e selecione **Executar...**
3. No pop-up do Run Connector, selecione o passo **de sincronização completa** e clique em **OK**.
4. Aguarde que o passo de sincronização completa esteja completo.
5. Repita os passos acima dos restantes Conectores AD se tiver mais de um Conector AD. Normalmente, são necessários vários conectores se tiver vários diretórios no local.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>Passo 6. Verifique se não há alterações inesperadas à espera de serem exportadas para a AD Azure
1. Vá ao separador **Conectores** no Gestor de Serviçode Sincronização.
2. Clique à direita no Conector **AD Azure** e selecione **Search Connector Space**.
3. No pop-up search Connector Space:
    1. Definir o âmbito para **a exportação pendente.**
    2. Verifique todas as 3 caixas de verificação, incluindo **Adicionar,** **Modificar** e **Apagar**.
    3. Clique no botão **'Procurar'** para devolver todos os objetos com alterações à espera de serem exportados para a AD Azure.
    4. Verifique se não há alterações inesperadas. Para examinar as alterações para um determinado objeto, clique duas vezes no objeto.

### <a name="step-7-export-the-changes-to-azure-ad"></a>Passo 7. Exportar as alterações para AD Azure
Para exportar as alterações para AD Azure:
1. Vá ao separador **Conectores** no Gestor de Serviçode Sincronização.
2. Clique à direita no Conector **Azure AD** e selecione **Executar...**
4. No pop-up run Connector, selecione **Passo de Exportação** e clique **OK**.
5. Aguarde que a Exportação para a AD Azure esteja concluída e confirme que não existem mais erros da LargeObject.

### <a name="step-8-re-enable-sync-scheduler"></a>Passo 8. Reativar programador de sincronização
Agora que a questão está resolvida, reativar o programador de sincronização incorporado:
1. Iniciar a sessão PowerShell.
2. Reativar a sincronização programada executando cmdlet:`Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> Os passos anteriores só se aplicam às versões mais recentes (1.1.xxx.x) do Azure AD Connect com o programador incorporado. Se estiver a utilizar versões mais antigas (1.0.xxx.x) do Azure AD Connect que utiliza o Programador de Tarefas do Windows, ou se estiver a utilizar o seu próprio programador personalizado (não é comum) para desencadear uma sincronização periódica, tem de os desativar em conformidade.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).

