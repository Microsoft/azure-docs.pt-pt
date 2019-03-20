---
title: O Azure AD Connect - erros de LargeObject causados pelo atributo userCertificate | Documentos da Microsoft
description: Este tópico fornece os passos de remediação para erros de LargeObject causados pelo atributo userCertificate.
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
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58096678"
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Sincronização do Azure AD Connect: Tratamento de erros de LargeObject causados pelo atributo userCertificate

O Azure AD impõe um limite máximo de **15** valores de certificado no **userCertificate** atributo. Se o Azure AD Connect exporta um objeto com mais de 15 valores para o Azure AD, o Azure AD retorna um **LargeObject** erro com a mensagem:

>*"O objeto aprovisionado é demasiado grande. Compacte o número de valores de atributo neste objeto. A operação será repetida no próximo ciclo de sincronização..."*

O erro de LargeObject pode ser causado por outros atributos de AD. Para confirmar o fato é causada pelo atributo userCertificate, tem de validar contra o objeto no AD no local ou no [pesquisa de Metaverso do Gestor do serviço de sincronização](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-mvsearch).

Para obter a lista de objetos no seu inquilino com erros de LargeObject, utilize um dos seguintes métodos:

 * Se o seu inquilino está ativado para o Azure AD Connect Health para sincronização, pode consultar o [relatório de erros de sincronização](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-sync) fornecido.
 
 * O e-mail de notificação para erros de sincronização de diretório que é enviado no final de cada ciclo de sincronização tem a lista de objetos com erros de LargeObject. 
 * O [guia de operações do Synchronization Service Manager](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-operations) apresenta a lista de objetos com erros de LargeObject se clicar a exportação de mais recente para a operação do Azure AD.
 
## <a name="mitigation-options"></a>Opções de atenuação
Até que o erro de LargeObject for resolvido, outras alterações de atributos para o mesmo objeto não não possível exportar para o Azure AD. Para resolver o erro, pode considerar as seguintes opções:

 * Atualizar o Azure AD Connect, para criar 1.1.524.0 ou depois. No Azure AD Connect crie 1.1.524.0, a sincronização de out-of-box regras foram atualizadas para não exportar atributos userCertificate e userSMIMECertificate, se os atributos têm mais de 15 valores. Para obter detalhes sobre como atualizar o Azure AD Connect, consulte o artigo [do Azure AD Connect: Upgrade from a previous version to the latest](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version) (Sincronização do Azure AD Connect: Atualizar de uma versão anterior para a mais recente).

 * Implementar um **regra de sincronização de saída** no Azure AD Connect que exporta um **nulo, valor em vez dos valores reais para objetos com mais de 15 valores de certificado**. Esta opção é adequada se não necessitar de qualquer um dos valores de certificado seja exportado para o Azure AD para objetos com mais de 15 valores. Para obter detalhes sobre como implementar esta regra de sincronização, consulte a secção seguinte [regra de sincronização de implementar para limitar a exportação de atributo userCertificate](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute).

 * Reduzir o número de valores de certificado no local de objeto do AD (15 ou menos), removendo os valores que já não estão em utilização pela sua organização. Isso é adequado se o inchaço de atributo é causado por certificados expirados ou não utilizados. Pode utilizar o [aqui disponível do script do PowerShell](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) para ajudar a localizar, criar cópias de segurança e eliminar certificados expirados no seu local AD. Antes de eliminar os certificados, recomenda-se que verifique com os administradores de infraestrutura de chave pública na sua organização.

 * Configure o Azure AD Connect para impedir que o atributo userCertificate a ser exportado para o Azure AD. Em geral, não recomendamos esta opção, uma vez que o atributo pode ser utilizado pelo Microsoft Online Services para ativar cenários específicos. Em particular:
    * O atributo userCertificate no objeto User é utilizado pelo Exchange Online e clientes do Outlook para assinatura e encriptação. Para saber mais sobre esta funcionalidade, consulte o artigo [S/MIME para assinatura e encriptação](https://technet.microsoft.com/library/dn626158(v=exchg.150).aspx).

    * O atributo userCertificate no objeto de computador é utilizado pelo Azure AD para permitir que o Windows 10 no local dispositivos associados a um domínio ligar ao Azure AD. Para saber mais sobre esta funcionalidade, veja artigo [ligar dispositivos associados a um domínio ao Azure AD para experiências do Windows 10](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy).

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>Implementando a regra de sincronização para limitar a exportação de atributo userCertificate
Para resolver o erro de LargeObject causado pelo atributo userCertificate, pode implementar uma regra de sincronização de saída no Azure AD Connect que exporta um **valor em vez dos valores reais para objetos com mais de 15 valores de certificadonulo**. Esta secção descreve os passos necessários para implementar a regra de sincronização para **utilizador** objetos. Os passos podem ser adaptados para **contacto** e **computador** objetos.

> [!IMPORTANT]
> Exportar um valor nulo remove o certificado de valores anteriormente exportado com êxito para o Azure AD.

Os passos podem ser resumidos como:

1. Desativar o agendador de sincronização e certifique-se de que não existe nenhuma sincronização em curso.
3. Encontre a regra de sincronização de saída existente para o atributo userCertificate.
4. Crie a regra de sincronização de saída necessária.
5. Certifique-se a nova regra de sincronização num objeto existente com o erro de LargeObject.
6. Aplica a nova regra de sincronização aos restantes objetos com o erro de LargeObject.
7. Certifique-se de que não foram efetuadas alterações inesperadas aguardando para ser exportados para o Azure AD.
8. Exporte as alterações para o Azure AD.
9. Voltar a ativar o agendador de sincronização.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Passo 1. Desativar o agendador de sincronização e certifique-se de que não existe nenhuma sincronização em curso
Certifique-se de que não ocorre sincronização ocorre enquanto estiver no meio de implementação de uma nova regra de sincronização para evitar alterações indesejadas a ser exportadas para o Azure AD. Para desativar o agendador de sincronização interna:
1. Inicie a sessão do PowerShell no servidor do Azure AD Connect.

2. Desative a sincronização agendada, executando o cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> Os passos anteriores apenas são aplicáveis a versões mais recentes (1.1.xxx.x) do Azure AD Connect com o agendador interno. Se estiver a utilizar as versões mais antigas (1.0.xxx.x) do Azure AD Connect que utiliza o agendador de tarefas do Windows, ou se estiver a utilizar o seu próprio agendador personalizado (não comuns) para acionar a sincronização periódica, terá de desativá-las em conformidade.

1. Iniciar o **Synchronization Service Manager** ao aceder ao serviço de sincronização de início →.

1. Vá para o **Operations** separador e confirme que não existe nenhuma operação cujo estado é *"em curso."*

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>Passo 2. Encontrar a regra de sincronização de saída existente para o atributo userCertificate
Deve haver uma regra de sincronização existente que está ativada e configurada para exportar o atributo userCertificate para objetos de utilizador para o Azure AD. Localize esta regra de sincronização para descobrir sua **precedência** e **filtro de âmbito** configuração:

1. Iniciar o **Editor de regras de sincronização** ao aceder ao Editor de regras de sincronização de início →.

2. Configure os filtros de pesquisa com os seguintes valores:

    | Atributo | Value |
    | --- | --- |
    | Direção |**Outbound** |
    | Tipo de objeto de MV |**Pessoa** |
    | Conector |*nome do conector do Azure AD* |
    | Tipo de objeto do conector |**user** |
    | Atributo de MV |**userCertificate** |

3. Se estiver a utilizar as regras de sincronização (out-of-box) de OOB para o conector do Azure AD para exportar o atributo de userCertficiate para objetos de utilizador, deve regressar a *"Out ao AAD – utilizador ExchangeOnline"* regra.
4. Tome nota da **precedência** valor desta regra de sincronização.
5. Selecione a regra de sincronização e clique em **editar**.
6. Na *"Editar reservado confirmação de regra"* caixa de diálogo pop-up, clique em **não**. (Não se preocupe, não vamos fazer qualquer alteração a esta regra de sincronização).
7. No ecrã de edição, selecione o **Scoping filtro** separador.
8. Tenha em atenção a configuração de filtro de âmbito. Se estiver a utilizar a regra de sincronização OOB, deverá exatamente existir **um grupo de filtro âmbito que contém dois cláusulas**, incluindo:

    | Atributo | Operador | Value |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Utilizador |
    | cloudMastered | NOTEQUAL | Verdadeiro |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>Passo 3. Criar a regra de sincronização de saída necessária
A nova regra de sincronização tem de ter o mesmo **filtro de âmbito** e **precedência superior** que a regra de sincronização existente. Isto garante que a nova regra de sincronização aplica-se para o mesmo conjunto de objetos, como a regra de sincronização existente e substitui a regra de sincronização existente para o atributo userCertificate. Para criar a regra de sincronização:
1. No Editor de regras de sincronização, clique nas **Adicionar nova regra** botão.
2. Sob o **separador de descrição**, forneça a seguinte configuração:

    | Atributo | Value | Detalhes |
    | --- | --- | --- |
    | Name | *Forneça um nome* | Por exemplo, *"Out ao AAD – personalizado para userCertificate substituição"* |
    | Descrição | *Forneça uma descrição* | Por exemplo, *"Se o atributo userCertificate tem mais de 15 valores, exportar NULL".* |
    | Sistema ligado | *Selecione o conector Azure AD* |
    | Tipo de objeto de sistema ligado | **user** | |
    | Tipo de objeto de Metaverso | **person** | |
    | Tipo de Ligação | **Associar** | |
    | Precedência | *Optou por um número entre 1 e 99* | O número escolhido não pode ser utilizado por qualquer regra de sincronização existente e tem um valor inferior (e, portanto, precedência superior) que a regra de sincronização existente. |

3. Vá para o **Scoping filtro** separador e implementar o mesmo filtro de âmbito a regra de sincronização existente está a utilizar.
4. Ignorar a **associar regras** separador.
5. Vá para o **transformações** separador para adicionar uma nova transformação com os seguintes configuração:

    | Atributo | Value |
    | --- | --- |
    | Tipo de fluxo |**expressão** |
    | Atributo de Destino |**userCertificate** |
    | Atributo de origem |*Utilize a seguinte expressão*: `IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. Clique nas **adicionar** botão para criar a regra de sincronização.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>Passo 4. Certifique-se a nova regra de sincronização num objeto existente com o erro de LargeObject
Isso é verificar se a regra de sincronização criada está funcionando corretamente num objeto do AD existente com o erro de LargeObject antes de aplicá-la a outros objetos:
1. Vá para o **operações** separador o Synchronization Service Manager.
2. Selecione a exportação de mais recente para a operação do Azure AD e clique em um dos objetos com erros de LargeObject.
3.  No ecrã de pop-up de propriedades de objeto de espaço conector, clique nas **pré-visualização** botão.
4. No ecrã de pop-up de pré-visualização, selecione **completa de sincronização** e clique em **confirmar pré-visualização**.
5. Feche o ecrã de pré-visualização e a tela de propriedades de objeto de espaço conector.
6. Vá para o **conectores** separador o Synchronization Service Manager.
7. Com o botão direito sobre os **do Azure AD** conector e selecione **executar...**
8. No pop-up executar o conector, selecione **exportar** passo e clique em **OK**.
9. Aguarde pela exportação para o Azure AD para concluir e confirme que não há nenhum erro de LargeObject mais neste objeto específico.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>Passo 5. Aplicar a nova regra de sincronização aos restantes objetos com o erro de LargeObject
Depois de adicionada a regra de sincronização, tem de executar um passo de sincronização completa no conector AD:
1. Vá para o **conectores** separador o Synchronization Service Manager.
2. Com o botão direito sobre os **AD** conector e selecione **executar...**
3. No pop-up executar o conector, selecione **sincronização completa** passo e clique em **OK**.
4. Aguarde que o passo de sincronização completa concluir.
5. Repita os passos acima para os conectores restantes do AD, se tiver mais do que um AD conectores. Normalmente, vários conectores são necessários se tiver vários diretórios no local.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>Passo 6. Certifique-se de que não foram efetuadas alterações inesperadas aguardando para ser exportados para o Azure AD
1. Vá para o **conectores** separador o Synchronization Service Manager.
2. Com o botão direito sobre os **do Azure AD** conector e selecione **procurar espaço conector**.
3. No pop-up procurar espaço conector:
    1. Definir o âmbito **pendentes exportação**.
    2. Verifique todos os 3 caixas de verificação, incluindo **Add**, **modificar** e **eliminar**.
    3. Clique em **pesquisa** botão para retornar todos os objetos com alterações em espera ser exportada para o Azure AD.
    4. Certifique-se de que não foram efetuadas alterações inesperadas. Para examinar as alterações para um determinado objeto, faça duplo clique no objeto.

### <a name="step-7-export-the-changes-to-azure-ad"></a>Passo 7. Exportar as alterações para o Azure AD
Para exportar as alterações para o Azure AD:
1. Vá para o **conectores** separador o Synchronization Service Manager.
2. Com o botão direito sobre os **do Azure AD** conector e selecione **executar...**
4. No pop-up executar o conector, selecione **exportar** passo e clique em **OK**.
5. Aguarde pela exportação para o Azure AD para concluir e confirme não existem erros de LargeObject mais.

### <a name="step-8-re-enable-sync-scheduler"></a>Passo 8. Volte a ativar o agendador de sincronização
Agora que o problema for resolvido, volte a ativar o agendador de sincronização interna:
1. Inicie a sessão do PowerShell.
2. Volte a Ativar sincronização agendada, executando o cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> Os passos anteriores apenas são aplicáveis a versões mais recentes (1.1.xxx.x) do Azure AD Connect com o agendador interno. Se estiver a utilizar as versões mais antigas (1.0.xxx.x) do Azure AD Connect que utiliza o agendador de tarefas do Windows, ou se estiver a utilizar o seu próprio agendador personalizado (não comuns) para acionar a sincronização periódica, terá de desativá-las em conformidade.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).

