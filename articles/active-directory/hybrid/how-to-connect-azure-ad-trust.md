---
title: Azure AD Connect - Gerencie a confiança da AD FS com a Azure AD utilizando o Azure AD Connect [ Ligação AD ] Microsoft Docs
description: Detalhes operacionais do manuseamento de confiança da Azure AD pela Ligação Azure AD.
keywords: AD FS, ADFS, Gestão AD FS, AAD Connect, Connect, Azure AD, trust, AAD, claim, claim, claim rules, emissão, transformação, regras, backup, restaurar
services: active-directory
documentationcenter: ''
ms.reviewer: anandyadavmsft
manager: daveba
ms.subservice: hybrid
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/28/2018
ms.author: billmath
author: billmath
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f3e521fb7668305ce511aaddd63ed2cce8dfed0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331726"
---
# <a name="manage-ad-fs-trust-with-azure-ad-using-azure-ad-connect"></a>Gerir a confiança do AD FS com o Azure AD com o Azure AD Connect

## <a name="overview"></a>Descrição geral

O Azure AD Connect pode gerir a federação entre o Ative Directory Federation Service (AD FS) e o Azure AD. Este artigo fornece uma visão geral de:

* As várias configurações configuradas sobre a confiança por Azure AD Connect
* A emissão transforma regras (regras de reivindicação) definidas pela Azure AD Connect
* Como fazer o back-up e restaurar as suas regras de reclamação entre atualizações e atualizações de configuração. 

## <a name="settings-controlled-by-azure-ad-connect"></a>Definições controladas por Azure AD Connect

O Azure AD Connect gere **apenas** as definições relacionadas com o fundo Azure AD. O Azure AD Connect não modifica quaisquer definições em outros fundos de partes que dependem em AD FS. A tabela seguinte indica definições que são controladas pelo Azure AD Connect.

| Definição | Descrição |
| :--- | :--- |
| Certificado de assinatura token | O Azure AD Connect pode ser usado para redefinir e recriar a confiança com o Azure AD. O Azure AD Connect faz um reversão imediato de certificados de assinatura de token para AD FS e atualiza as definições da federação de domínio Azure AD.|
| Algoritmo de assinatura token | A Microsoft recomenda a utilização do SHA-256 como algoritmo de assinatura simbólica. O Azure AD Connect pode detetar se o algoritmo de assinatura de símbolos for definido para um valor menos seguro do que o SHA-256. Atualizará a definição para SHA-256 na próxima operação de configuração possível. Outra confiança do partido deve ser atualizada para utilizar o novo certificado de assinatura simbólica. |
| Identificador de confiança da Azure AD | O Azure AD Connect define o valor correto do identificador para o fundo Azure AD. A AD FS identifica exclusivamente a confiança da AD Azure utilizando o valor do identificador. |
| Pontos finais da AD Azure | O Azure AD Connect garante que os pontos finais configurados para a confiança azure AD são sempre de acordo com os valores recomendados mais recentes para a resiliência e desempenho. |
| Emissão transforma regras | Existem números de regras de reclamação que são necessárias para um desempenho ótimo das características do Azure AD num cenário federado. O Azure AD Connect garante que a confiança azure AD está sempre configurada com o conjunto certo de regras de reclamação recomendadas. |
| Id alternativo | Se a sincronização estiver configurada para utilizar um id alternativo, o Azure AD Connect configura a AD FS para executar a autenticação utilizando um id alternativo. |
| Atualização automática de metadados | A confiança com o Azure AD está configurada para a atualização automática de metadados. A AD FS verifica periodicamente os metadados da confiança da AD Azure e mantém-no atualizado caso este se mude no lado da AD Azure. |
| Autenticação Integrada do Windows (IWA) | Durante a operação de adesão da Hybrid Azure AD, a IWA está ativada para o registo do dispositivo para facilitar a adesão da Hybrid Azure AD a dispositivos de nível inferior |

## <a name="execution-flows-and-federation-settings-configured-by-azure-ad-connect"></a>Fluxos de execução e configurações da federação configuradas por Azure AD Connect

A ligação Azure AD não atualiza todas as definições para a confiança da AD Azure durante os fluxos de configuração. As definições modificadas dependem da execução do fluxo de tarefas ou execução. A tabela seguinte enumera as definições impactadas em diferentes fluxos de execução.

| Fluxo de execução | Definições impactadas |
| :--- | :--- |
| Instalação de primeiro passe (expresso) | Nenhuma |
| Instalação de primeiro passe (nova quinta AD FS) | Uma nova quinta AD FS é criada e uma confiança com a Azure AD é criada de raiz. |
| Instalação de primeiro passe (exploração ad FS existente, fundo Azure AD existente) | Identificador de confiança Azure AD, Isence transforme regras, pontos finais da AD Azure, Alternad (se necessário), atualização automática de metadados |
| Redefinir a confiança da AD Azure | Certificado de assinatura Token, algoritmo de assinatura Token, identificador de confiança Azure AD, Isence transforme regras, pontos finais de AD Azure, Atualização de metadados alternativos (se necessário), atualização automática de metadados |
| Adicionar servidor da federação | Nenhuma |
| Adicionar servidor WAP | Nenhuma |
| Opções do dispositivo | Emissão transforma regras, IWA para registo de dispositivos |
| Adicionar domínio federado | Se o domínio for adicionado pela primeira vez, ou seja, a configuração está a mudar de federação de domínio único para federação de vários domínios – o Azure AD Connect recriará a confiança do zero. Se a confiança com a AD Azure já estiver configurada para vários domínios, apenas as regras de transformação da Emissão são modificadas |
| Atualizar TLS | Nenhuma |

Durante todas as operações, em que qualquer definição é modificada, o Azure AD Connect faz uma cópia de segurança das definições de confiança atual em **%ProgramData%\AADConnect\ADFS**

![Página Azure AD Connect mostrando mensagem sobre o backup de confiança da Azure AD existente](./media/how-to-connect-azure-ad-trust/backup2.png)

> [!NOTE]
> Antes da versão 1.1.873.0, a cópia de segurança consistia apenas em regras de transformação de emissão e foram apoiadas no ficheiro de registo de rastreio do assistente.

## <a name="issuance-transform-rules-set-by-azure-ad-connect"></a>Emissão transforma regras definidas pela Azure AD Connect

O Azure AD Connect garante que a confiança azure AD está sempre configurada com o conjunto certo de regras de reclamação recomendadas. A Microsoft recomenda a utilização da ligação Azure AD para gerir o seu fundo Azure AD. Esta secção enumera as regras de transformação da emissão definidas e a sua descrição.

| Nome da regra | Descrição |
| --- | --- |
| Emissão UPN | Esta regra questiona o valor do nome do utilizador a partir do atributo configurado em definições de sincronização para o nome principal do utilizador.|
| Objectguid e msdsconsistência para alegação imutável personalizada | Esta regra adiciona um valor temporário no gasoduto para o valor objectguia e msdsconsistência se existir |
| Verifique a existência de msdsconsistêncialguid | Com base no facto de o valor para a msdsconsistêncialguid existir ou não, definimos uma bandeira temporária para direcionar o que usar como ImutávelId |
| Emitir msdsconsistêncialguid como ID imutável se existir | Emitir msdsconsistência lifaguidas como ImutableId se o valor existir |
| Emitir objetoGuidRule se a regra msdsConsistênciaNão existe | Se o valor para a msdsconsistêncianão existir, o valor do objectguia será emitido como ImutávelId |
| Identificador de nome sitiem | Esta regra emite valor para a alegação de identificação de nomes.|
| Número de contato de emissão para computadores unidos pelo domínio | Se a entidade que está a ser autenticada for um dispositivo de domínio unido, esta regra emite o tipo de conta como DJ que significa um dispositivo de domínio unido |
| Conta de problemasTipo com o utilizador de valor quando não é uma conta de computador | Se a entidade autenticada for um utilizador, esta regra emite o tipo de conta como Utilizador |
| Emitir emitida quando não é uma conta de computador | Esta regra emite o valor emitente Quando a entidade autenticadora não é um dispositivo. O valor é criado através de um regex, que é configurado pelo Azure AD Connect. O regex é criado depois de ter em conta todos os domínios federados usando O Azure AD Connect. |
| Emissão emitida para DJ computador auth | Esta regra emite o valor emitente Quando a entidade autenticadora é um dispositivo |
| Emitir onexobjectguid para computadores ligados ao domínio | Se a entidade que está a ser autenticada for um dispositivo de domínio unido, esta regra emite o objectguia no local para o dispositivo |
| Passar pelo SID primário | Esta regra emite o SID primário da entidade autenticadora |
| Passar por reclamação - insideCorporateNetwork | Esta regra emite uma reivindicação que ajuda a Azure AD a saber se a autenticação vem de dentro da rede corporativa ou externamente |
| Passe por Reivindicação – Psso |   |
| Emitir Reclamações de validade da palavra-passe | Esta regra emite três pedidos de tempo de validade da palavra-passe, número de dias para a palavra-passe expirar da entidade a ser autenticada e URL onde se encaminhar para a alteração da palavra-passe.|
| Passar por reivindicação - referências authnmethods | O valor da reclamação emitida ao abrigo desta regra indica que tipo de autenticação foi realizada para a entidade |
| Passar por reivindicação - multifactorauthenticationinstant | O valor desta reclamação especifica o tempo, na UTC, quando o utilizador realizou pela última vez a autenticação de vários fatores. |
| Passar por reclamação - AlternateLoginID | Esta regra emite a alegação AlternateLoginID se a autenticação foi realizada usando identificação de login alternativa. |

> [!NOTE]
> As regras de reclamação para issue UPN e ImutableId diferirão se utilizar a escolha não predefinida durante a configuração Azure AD Connect

## <a name="restore-issuance-transform-rules"></a>Restaurar a emissão transforma regras

A versão 1.1.873.0 do Azure AD Connect faz uma cópia de segurança das definições de confiança do Azure AD sempre que é feita uma atualização para as definições de confiança da AD Azure. As definições de confiança da AD Azure são apoiadas em **%ProgramData%\AADConnect\ADFS**. O nome do ficheiro está no seguinte&lt;&gt;-&lt;formato AadTrust- data data&gt;.txt, por exemplo - AadTrust-20180710-150216.txt

![Uma imagem de exemplo de volta da confiança da AD Azure](./media/how-to-connect-azure-ad-trust/backup.png)

Você pode restaurar as regras de transformação da emissão usando os passos sugeridos abaixo

1. Abra a UI de gestão aD FS em Server Manager
2. Abra as propriedades fiduciárias da AD Azur ao passar a **AD &gt; FS Relying Party &gt; Trusts Microsoft Office 365 Identity Platform &gt; Edit Claims Isence Policy**
3. Clique na **regra adicionar**
4. No modelo de regra de reclamação, selecione Enviar Reclamações Usando uma Regra Personalizada e clique **em Seguinte**
5. Copie o nome da regra de reclamação do ficheiro de cópia de segurança e cole-o no nome da **regra de reclamação** de campo
6. Copie a regra de reclamação do ficheiro de cópia de segurança para o campo de texto para **a regra personalizada** e clique em **Terminar**

> [!NOTE]
> Certifique-se de que as suas regras adicionais não entram em conflito com as regras configuradas pelo Azure AD Connect.

## <a name="next-steps"></a>Passos seguintes
* [Gerir e personalizar serviços da Federação de Diretório Ativo utilizando o Azure AD Connect](how-to-connect-fed-management.md)
