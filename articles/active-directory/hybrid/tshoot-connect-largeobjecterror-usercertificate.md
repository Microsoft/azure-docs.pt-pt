---
title: Azure AD Connect - Erros de LargeObject causados pelo atributo userCertificate / Microsoft Docs
description: Este tópico fornece os passos de reparação para erros do LargeObject causados pelo atributo userCertificate.
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
ms.topic: troubleshooting
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: d33b419e0f24201d661ad0f5f1373022ea6e9e9f
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861753"
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Azure AD Connect sync: Handling LargeObject erros causados pelo atributo userCertificate

A Azure AD impõe um limite máximo de **15** valores de certificado no atributo **userCertificate.** Se o Azure AD Connect exportar um objeto com mais de 15 valores para Azure AD, a AD Azure devolve um erro **do LargeObject** com mensagem:

>*"O objeto provisionado é muito grande. Corte o número de valores de atributos neste objeto. A operação será novamente julgada no próximo ciclo de sincronização..."*

O erro do LargeObject pode ser causado por outros atributos AD. Para confirmar que é realmente causado pelo atributo userCertificate, é necessário verificar contra o objeto, quer no local, quer no Serviço de [Sincronização Metaverse Search](./how-to-connect-sync-service-manager-ui-mvsearch.md).

Para obter a lista de objetos no seu inquilino com erros do LargeObject, utilize um dos seguintes métodos:

 * Se o seu inquilino estiver habilitado para sincronização Azure AD Connect Health, pode consultar o Relatório de Erro de [Sincronização](./how-to-connect-health-sync.md) fornecido.
 
 * O e-mail de notificação para erros de sincronização de diretórios que é enviado no final de cada ciclo de sincronização tem a lista de objetos com erros do LargeObject. 
 * O [separador De Operações do Gestor de Serviços de Sincronização](./how-to-connect-sync-service-manager-ui-operations.md) apresenta a lista de objetos com erros do LargeObject se clicar na última operação Export to AD Azure.
 
## <a name="mitigation-options"></a>Opções de mitigação
Até que o erro do LargeObject seja resolvido, outras alterações de atributos no mesmo objeto não podem ser exportadas para Azure AD. Para resolver o erro, pode considerar as seguintes opções:

 * Atualize o Azure AD Connect para construir 1.1.524.0 ou depois. No Azure AD Connect constroem 1.1.524.0, as regras de sincronização fora da caixa foram atualizadas para não exportar atributos do utilizadorCertificado e utilizadorSMIMECertificam se os atributos tiverem mais de 15 valores. Para obter mais informações sobre como atualizar o Azure AD Connect, consulte o artigo [Azure AD Connect: Upgrade de uma versão anterior para a mais recente](./how-to-upgrade-previous-version.md).

 * Implementar uma **regra de sincronização de saída** no Azure AD Connect que exporta um valor **nulo em vez dos valores reais para objetos com mais de 15 valores de certificado**. Esta opção é adequada se não necessitar que nenhum dos valores do certificado seja exportado para Azure AD para objetos com mais de 15 valores. Para obter mais informações sobre como implementar esta regra de sincronização, consulte a secção seguinte [Implementando a regra de sincronização para limitar a exportação do atributo userCertificate](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute).

 * Reduza o número de valores de certificado no objeto AD no local (15 ou menos) removendo valores que já não estão a ser utilizados pela sua organização. Isto é adequado se o atributo inchar for causado por certificados caducados ou não utilizados. Pode utilizar o [script PowerShell disponível aqui](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) para ajudar a encontrar, fazer backup e eliminar certificados expirados no seu AD no local. Antes de apagar os certificados, recomenda-se que verifique com os administradores da Infraestrutura de Chave Pública na sua organização.

 * Configure Azure AD Connect para excluir o atributo userCertificate de ser exportado para Azure AD. Em geral, não recomendamos esta opção, uma vez que o atributo pode ser utilizado pelos Serviços Online da Microsoft para permitir cenários específicos. Em particular:
    * O atributo userCertificate no objeto Utilizador é utilizado pelos clientes Exchange Online e Outlook para a assinatura de mensagens e encriptação. Para saber mais sobre esta funcionalidade, consulte o artigo [S/MIME para a assinatura de mensagens e encriptação.](/microsoft-365/security/office-365-security/s-mime-for-message-signing-and-encryption)

    * O atributo userCertificate no objeto Computador é utilizado pela Azure AD para permitir que os dispositivos ligados ao domínio do Windows 10 se conectem ao Azure AD. Para saber mais sobre esta funcionalidade, consulte o artigo [Conecte dispositivos ligados ao domínio para Azure AD para experiências com o Windows 10](../devices/hybrid-azuread-join-plan.md).

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>Implementação da regra de sincronização para limitar a exportação do atributo userCertate
Para resolver o erro do LargeObject causado pelo atributo userCertificate, pode implementar uma regra de sincronização de saída no Azure AD Connect que exporta um **valor nulo em vez dos valores reais para objetos com mais de 15 valores de certificado**. Esta secção descreve os passos necessários para implementar a regra de sincronização dos objetos **do Utilizador.** Os passos podem ser adaptados para objetos **de contacto** e **computador.**

> [!IMPORTANT]
> A exportação de valor nulo elimina os valores dos certificados anteriormente exportados com sucesso para a Azure AD.

Os passos podem ser resumidos como:

1. Desative o programador de sincronização e verifique se não existe sincronização em curso.
3. Encontre a regra de sincronização de saída existente para o atributo userCertificate.
4. Crie a regra de sincronização de saída necessária.
5. Verifique a nova regra de sincronização de um objeto existente com erro LargeObject.
6. Aplique a nova regra de sincronização aos objetos restantes com erro LargeObject.
7. Verifique se não há alterações inesperadas à espera de serem exportadas para a Azure AD.
8. Exportar as alterações para Azure AD.
9. Re-ativar o programador de sincronização.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Passo 1. Desative o programador de sincronização e verifique se não há sincronização em curso
Certifique-se de que não ocorre sincronização enquanto estiver a implementar uma nova regra de sincronização para evitar que alterações não intencionais sejam exportadas para a AZure AD. Para desativar o programador de sincronização incorporado:
1. Iniciar sessão PowerShell no servidor Azure AD Connect.

2. Desative a sincronização programada executando o cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> Os passos anteriores só são aplicáveis às versões mais recentes (1.1.xxx.x) do Azure AD Connect com o programador incorporado. Se estiver a utilizar versões mais antigas (1.0.xxx.x) do Azure AD Connect que utiliza o Windows Task Scheduler, ou se estiver a utilizar o seu próprio programador personalizado (não comum) para desencadear uma sincronização periódica, tem de as desativar em conformidade.

1. Inicie o **Gestor de Serviços de Sincronização** indo para o START → Serviço de Sincronização.

1. Vá ao separador **Operações** e confirme que não há nenhuma operação cujo estado está *"em curso".*

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>Passo 2. Encontre a regra de sincronização de saída existente para o atributo userCertate
Deve existir uma regra de sincronização existente que esteja ativada e configurada para exportar o atributo de utilizadorCertificado para objetos do Utilizador para Azure AD. Localize esta regra de sincronização para descobrir a sua **precedência** e configuração do filtro de **deteção:**

1. Inicie o **Editor de Regras de Sincronização** indo ao START → Synchronization Rules Editor.

2. Configure os filtros de pesquisa com os seguintes valores:

    | Atributo | Valor |
    | --- | --- |
    | Direção |**Saída** |
    | Tipo de objeto MV |**Pessoa** |
    | Conector |*nome do seu conector AZURE AD* |
    | Tipo de objeto de conector |**utilizador** |
    | Atributo MV |**userCertificate** |

3. Se estiver a utilizar regras de sincronização OOB (fora de caixa) para o conector Azure AD para exportar o atributo de utilizadorCertficiato para objetos do Utilizador, deve voltar a utilizar a regra *"out to AAD – User ExchangeOnline".*
4. Note o valor de **precedência** desta regra de sincronização.
5. Selecione a regra de sincronização e clique em **Editar.**
6. No diálogo pop-up *"Editar a Confirmação* de Regras Reservadas", clique em **No**. (Não se preocupe, não vamos fazer qualquer alteração a esta regra de sincronização).
7. No ecrã de edição, selecione o **separador filtro Descosutilar.**
8. Note a configuração do filtro de escotagem. Se estiver a utilizar a regra de sincronização OOB, deve existir exatamente **um grupo de filtros contendo duas cláusulas,** incluindo:

    | Atributo | Operador | Valor |
    | --- | --- | --- |
    | sourceObjectType | IGUAL | Utilizador |
    | cloudMastered | NOTAQUAL | Verdadeiro |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>Passo 3. Criar a regra de sincronização de saída necessária
A nova regra de sincronização deve ter o mesmo **filtro de deteção** e **precedência mais elevada** do que a regra de sincronização existente. Isto garante que a nova regra de sincronização se aplica ao mesmo conjunto de objetos que a regra de sincronização existente e substitui a regra de sincronização existente para o atributo userCertificate. Para criar a regra de sincronização:
1. No Editor de Regras de Sincronização, clique no botão **Adicionar nova regra.**
2. No **separador Descrição,** forneça a seguinte configuração:

    | Atributo | Valor | Detalhes |
    | --- | --- | --- |
    | Nome | *Fornecer um nome* | Por exemplo, *"out to AAD – Substituição personalizada para o utilizadorCertificate"* |
    | Descrição | *Fornecer uma descrição* | Por exemplo, *"Se o atributo userCertificate tiver mais de 15 valores, exporte NU."* |
    | Sistema Conectado | *Selecione o Conector AD Azure* |
    | Tipo de objeto de sistema conectado | **utilizador** | |
    | Tipo de objeto metaverso | **pessoa** | |
    | Tipo de ligação | **Join** | |
    | Precedência | *Escolha um número entre 1 e 99* | O número escolhido não deve ser utilizado por qualquer regra de sincronização existente e tem um valor mais baixo (e, portanto, precedência superior) do que a regra de sincronização existente. |

3. Vá ao **separador filtro de deteção** e implemente o mesmo filtro de deteção que a regra de sincronização existente está a utilizar.
4. Ignore o separador **regras de Junção.**
5. Aceda ao separador **Transformações** para adicionar uma nova transformação utilizando a seguinte configuração:

    | Atributo | Valor |
    | --- | --- |
    | Tipo de Fluxo |**Expression** |
    | Atributo-alvo |**userCertificate** |
    | Atributo de origem |*Utilize a seguinte expressão:*`IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. Clique no botão **Adicionar** para criar a regra de sincronização.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>Passo 4: Verifique a nova regra de sincronização de um objeto existente com erro do LargeObject
Isto é para verificar se a regra de sincronização criada está a funcionar corretamente num objeto AD existente com erro LargeObject antes de a aplicar a outros objetos:
1. Aceda ao separador **Operações** no Gestor de Serviços de Sincronização.
2. Selecione a mais recente operação Export to AD Azure e clique num dos objetos com erros do LargeObject.
3.  No ecrã pop-up Do Objeto de Espaço do Conector, clique no botão **Pré-visualização.**
4. No ecrã pop-up preview, selecione **a sincronização completa** e clique em **"Vertreia de Compromisso".**
5. Feche o ecrã de pré-visualização e o ecrã "Propriedades do Objeto espacial do Conector".
6. Aceda ao **separador Conectores** no Gestor de Serviço de Sincronização.
7. Clique com o botão direito no **Conector AD Azure** e selecione **Executar...**
8. No pop-up do Run Connector, selecione **Passo de exportação** e clique **em OK**.
9. Aguarde que a Exportação para Azure AD complete e confirme que não existe mais erro do LargeObject neste objeto específico.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>Passo 5. Aplique a nova regra de sincronização aos objetos restantes com erro LargeObject
Uma vez adicionada a regra de sincronização, é necessário executar um passo de sincronização total no Conector AD:
1. Aceda ao **separador Conectores** no Gestor de Serviço de Sincronização.
2. Clique com o botão direito no Conector **AD** e selecione **Executar...**
3. No pop-up do Run Connector, selecione o passo **de sincronização completa** e clique **em OK**.
4. Aguarde que o passo de sincronização completa esteja concluído.
5. Repita os passos acima para os restantes Conectores AD se tiver mais de um Conectores AD. Normalmente, são necessários vários conectores se tiver vários diretórios no local.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>Passo 6. Verifique se não há alterações inesperadas à espera de serem exportadas para a Azure AD
1. Aceda ao **separador Conectores** no Gestor de Serviço de Sincronização.
2. Clique com o botão direito no **Conector AD Azure** e selecione **Search Connector Space**.
3. No pop-up do Espaço do Conector de Busca:
    1. Definir âmbito de **exportação pendente**.
    2. Verifique todas as 3 caixas de verificação, incluindo **Adicionar,** **Modificar** e **Eliminar**.
    3. Clique no botão **Procurar** para devolver todos os objetos com alterações à espera de serem exportadas para Azure AD.
    4. Verifique se não há alterações inesperadas. Para examinar as alterações para um determinado objeto, clique duas vezes no objeto.

### <a name="step-7-export-the-changes-to-azure-ad"></a>Passo 7. Exportar as alterações para Azure AD
Para exportar as alterações para Azure AD:
1. Aceda ao **separador Conectores** no Gestor de Serviço de Sincronização.
2. Clique com o botão direito no **Conector AD Azure** e selecione **Executar...**
4. No pop-up do Run Connector, selecione **Passo de exportação** e clique **em OK**.
5. Aguarde que a Export to Azure AD complete e confirme que não existem mais erros do LargeObject.

### <a name="step-8-re-enable-sync-scheduler"></a>Passo 8. Re-ativar o programador de sincronização
Agora que a questão está resolvida, reecamia o programador de sincronização incorporado:
1. Inicie a sessão PowerShell.
2. Re-activar a sincronização programada executando o cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> Os passos anteriores só são aplicáveis às versões mais recentes (1.1.xxx.x) do Azure AD Connect com o programador incorporado. Se estiver a utilizar versões mais antigas (1.0.xxx.x) do Azure AD Connect que utiliza o Windows Task Scheduler, ou se estiver a utilizar o seu próprio programador personalizado (não comum) para desencadear uma sincronização periódica, tem de as desativar em conformidade.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
