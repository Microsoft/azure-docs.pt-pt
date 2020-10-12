---
title: Azure AD Connect - Gerir a confiança da AD FS com a AZure AD usando a Azure AD Connect / Microsoft Docs
description: Detalhes operacionais do manuseamento de confiança Azure AD pela ligação Azure AD.
keywords: AD FS, ADFS, Gestão de FS AD, AAD Connect, Connect, Azure AD, trust, AAD, reivindicação, regras de reclamação, emissão, transformação, regras, backup, restauro
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
ms.topic: how-to
ms.date: 07/28/2018
ms.author: billmath
author: billmath
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13d56ec321cd257412c2b0abbe0be655c6cb4dbf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85360100"
---
# <a name="manage-ad-fs-trust-with-azure-ad-using-azure-ad-connect"></a>Gerir a confiança do AD FS com o Azure AD com o Azure AD Connect

## <a name="overview"></a>Descrição geral

O Azure AD Connect pode gerir a federação entre o Serviço de Federação de Diretórios Ativos (AD FS) e a Azure AD. Este artigo fornece uma visão geral de:

* As várias configurações configuradas na confiança por Azure AD Connect
* As regras de transformação de emissão (regras de reclamação) definidas pelo Azure AD Connect
* Como fazer o back-up e restaurar as suas regras de reclamação entre atualizações e atualizações de configuração. 

## <a name="settings-controlled-by-azure-ad-connect"></a>Configurações controladas por Azure AD Connect

O Azure AD Connect gere **apenas** as definições relacionadas com a confiança AD AZure. O Azure AD Connect não modifica quaisquer definições de outras fidedignidades de partes dependentes em FS AD. A tabela a seguir indica as definições que são controladas pelo Azure AD Connect.

| Definições | Descrição |
| :--- | :--- |
| Certificado de assinatura token | O Azure AD Connect pode ser usado para reiniciar e recriar a confiança com a Azure AD. O Azure AD Connect faz uma transição imediata única dos certificados de assinatura de token para AD FS e atualiza as definições da federação de domínio AD AZure.|
| Algoritmo de assinatura token | A Microsoft recomenda a utilização do SHA-256 como algoritmo de assinatura de fichas. O Azure AD Connect pode detetar se o algoritmo de assinatura de fichas está definido para um valor menos seguro do que SHA-256. Atualizará a definição para SHA-256 na próxima operação de configuração possível. Outras pessoas que confiam na confiança das partes devem ser atualizadas para utilizar o novo certificado de assinatura simbólica. |
| Identificador de confiança AD AZure | Azure AD Connect define o valor correto do identificador para a confiança AD Azure. A AD FS identifica exclusivamente a confiança Azure AD usando o valor do identificador. |
| Pontos finais da AD AZure | O Azure AD Connect garante que os pontos finais configurados para a confiança AD AZure são sempre de acordo com os valores recomendados mais recentes para a resiliência e desempenho. |
| Regras de transformação de emissão | Existem números de regras de reclamação que são necessárias para um melhor desempenho das características do Azure AD em um ambiente federado. O Azure AD Connect garante que o fundo Azure AD está sempre configurado com o conjunto certo de regras de reclamação recomendadas. |
| Id alternativo | Se a sincronização estiver configurada para utilizar id alternativo, o Azure AD Connect configura a AD FS para efetuar a autenticação utilizando id alternativo. |
| Atualização automática de metadados | A confiança com a Azure AD está configurada para a atualização automática de metadados. A AD FS verifica periodicamente os metadados da confiança AD AD Azure e mantém-no atualizado no caso de mudar no lado AD Azure. |
| Autenticação integrada do Windows (IWA) | Durante a operação de junção AZure AD híbrida, a IWA está habilitada para o registo do dispositivo para facilitar a junção híbrida Azure AD para dispositivos de baixo nível |

## <a name="execution-flows-and-federation-settings-configured-by-azure-ad-connect"></a>Fluxos de execução e configurações da federação configuradas pelo Azure AD Connect

A ligação AD AD azul não atualiza todas as definições para a confiança AD AZure durante os fluxos de configuração. As definições modificadas dependem de que tarefa ou fluxo de execução está a ser executado. A tabela que se segue lista as definições impactadas em diferentes fluxos de execução.

| Fluxo de execução | Configurações impactadas |
| :--- | :--- |
| Instalação do primeiro passe (expresso) | Nenhum |
| Instalação do primeiro passe (nova quinta AD FS) | Uma nova fazenda AD FS é criada e um fundo com Azure AD é criado de raiz. |
| Instalação de primeiro passe (fazenda AD FS existente, confiança AD existente) | Identificador de confiança Azure AD, regras de transformação de emissão, pontos finais Azure AD, Id alternativo (se necessário), atualização automática de metadados |
| Repor a confiança da AD AD | Certificado de assinatura token, algoritmo de assinatura token, identificador de confiança AZure AD, regras de transformação de emissão, pontos finais AZure AD, Id alternativo (se necessário), atualização automática de metadados |
| Adicionar servidor da federação | Nenhum |
| Adicionar servidor WAP | Nenhum |
| Opções do dispositivo | Regras de transformação de emissão, IWA para registo de dispositivos |
| Adicionar domínio federado | Se o domínio for adicionado pela primeira vez, isto é, a configuração está a mudar de uma federação de domínio único para federação de vários domínios – o Azure AD Connect recriará a confiança do zero. Se a confiança com a AD Azure já estiver configurada para vários domínios, apenas as regras de transformação de emissão são modificadas |
| Atualizar TLS | Nenhum |

Durante todas as operações, em que qualquer configuração é modificada, o Azure AD Connect faz uma cópia de segurança das definições de confiança atuais em **%ProgramData%\AADConnect\ADFS**

![Página Azure AD Connect mostrando mensagem sobre a cópia de segurança da AD AZure existente](./media/how-to-connect-azure-ad-trust/backup2.png)

> [!NOTE]
> Antes da versão 1.1.873.0, a cópia de segurança consistia apenas em regras de transformação de emissão e foram apoiadas no ficheiro de registo de registo de rastreios de assistentes.

## <a name="issuance-transform-rules-set-by-azure-ad-connect"></a>Regras de transformação de emissão definidas por Azure AD Connect

O Azure AD Connect garante que o fundo Azure AD está sempre configurado com o conjunto certo de regras de reclamação recomendadas. A Microsoft recomenda a utilização de um azure AD para gerir a sua confiança AD Azure. Esta secção lista as regras de transformação de emissão definidas e a sua descrição.

| Nome da regra | Descrição |
| --- | --- |
| Emissão UPN | Esta regra questiona o valor do nome do utilizador a partir do atributo configurado em sincronização para o nome de utilizador.|
| Guia de objectil e msdsconsistincyguid para reclamação imutável personalizada | Esta regra adiciona um valor temporário no pipeline para o valor do objectguid e msdsconsistencyguid se existir |
| Verifique a existência de msdsconsistencyguid | Baseado em se o valor para msdsconsistincyguid existe ou não, definimos uma bandeira temporária para direcionar o que usar como Imutável |
| Emite msdsconsistencyguid como Imutável ID se existir | Emite msdsconsistencyguid como ImuttableId se o valor existir |
| Emitir objectGuidRule se msdsConsistencyGuid regra não existe | Se o valor para msdsconsisitncyguid não existir, o valor do objectguid será emitido como Imutável |
| Emissão de identificador | Esta regra emite valor para a reclamação do identificador de nomes.|
| Emitir contabilidade para computadores unidos pelo domínio | Se a entidade que está a ser autenticada for um dispositivo de união de domínio, esta regra emite o tipo de conta como DJ significando um dispositivo de união de domínio |
| Emitir ContaType com o utilizador de valor quando não é uma conta de computador | Se a entidade que está a ser autenticada for um utilizador, esta regra emite o tipo de conta como Utilizador |
| Emitir problema quando não é uma conta de computador | Esta regra emite o valor emitente Quando a entidade autenticadora não é um dispositivo. O valor é criado através de um regex, que é configurado por Azure AD Connect. O regex é criado depois de ter em conta todos os domínios federados usando Azure AD Connect. |
| Emissão de emissão para DJ computer auth | Esta regra emite o valor emitente Quando a entidade autenticadora é um dispositivo |
| Emissão onpremobjectguid para computadores ligados a domínios | Se a entidade que está a ser autenticada for um dispositivo de união de domínios, esta regra emite o objectguid no local para o dispositivo |
| Passar pelo SID primário | Esta regra emite o SID primário da entidade autenticadora |
| Passe através da reivindicação - insideCorporateNetwork | Esta regra emite uma alegação que ajuda a Azure AD a saber se a autenticação vem de dentro da rede corporativa ou externamente |
| Passe através da reivindicação - Psso |   |
| Emitir Pedidos de Expiração da Palavra-Passe | Esta regra emite três pedidos de tempo de validade da palavra-passe, número de dias para a palavra-passe expirar da entidade a ser autenticada e URL onde fazer a rota para alterar a palavra-passe.|
| Passe pela reivindicação - authnmethodsreferences | O valor da reclamação emitida ao abrigo desta regra indica que tipo de autenticação foi realizada para a entidade |
| Passe por reivindicação - multifactorauthenticationinstant | O valor desta reclamação especifica a hora, na UTC, quando o utilizador realizou pela última vez a autenticação de vários fatores. |
| Passe através da reclamação - AlternateLoginID | Esta regra emite a alegação AlternateLoginID se a autenticação foi realizada utilizando o ID de login alternativo. |

> [!NOTE]
> As regras de reclamação para a Emissão UPN e ImuttableId diferirão se utilizar escolha não padrão durante a configuração AZURE AD Connect

## <a name="restore-issuance-transform-rules"></a>Restaurar regras de transformação de emissão

Azure AD Connect versão 1.1.873.0 ou posterior faz uma cópia de segurança das definições de confiança AD AZure sempre que uma atualização é feita para as definições de confiança AD Azure. As definições de confiança AD AZure são apoiadas em **%ProgramData%\\AADConnect\ADFS**. O nome do ficheiro está no seguinte formato AadTrust- &lt; hora da data &gt; - &lt; &gt; .txt, por exemplo - AadTrust-20180710-150216.txt

![Uma imagem de exemplo de volta da confiança AD AD Azure](./media/how-to-connect-azure-ad-trust/backup.png)

Pode restaurar as regras de transformação da emissão usando os passos sugeridos abaixo

1. Abra o UI de gestão de FS AD no Server Manager
2. Abra as propriedades de confiança Azure AD indo **AD FS &gt; Relying Party Trusts &gt; Microsoft Office 365 Identity Platform &gt; Edit Claims Policy**
3. Clique na **regra de adicionar**
4. No modelo de regra de reclamação, selecione Enviar Reclamações Usando uma Regra Personalizada e clique em **Seguinte**
5. Copie o nome da regra de reclamação a partir do ficheiro de backup e cole-o no campo **Nome da regra de reclamação**
6. Copie a regra de reclamação do ficheiro de backup no campo de texto para **a regra personalizada** e clique em **Terminar**

> [!NOTE]
> Certifique-se de que as suas regras adicionais não entram em conflito com as regras configuradas pelo Azure AD Connect.

## <a name="next-steps"></a>Passos seguintes
* [Gerir e personalizar serviços da Federação de Diretórios Ativos utilizando o Azure AD Connect](how-to-connect-fed-management.md)
