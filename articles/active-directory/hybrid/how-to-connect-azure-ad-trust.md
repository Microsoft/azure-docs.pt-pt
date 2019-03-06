---
title: O Azure AD Connect - gerir a confiança do AD FS com o Azure AD com o Azure AD Connect | Documentos da Microsoft
description: Detalhes operacionais de confiança do Azure AD processamento pelo Azure AD connect.
keywords: AD FS, ADFS, gestão do AD FS, o AAD Connect, Connect, do Azure AD, confiança, AAD, de afirmação, de afirmações, reclamar regras, emissão, transformação, regras, cópia de segurança, restauro
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
ms.openlocfilehash: 8bd46bb820c7127c4fa6105fcc0be73bb66024c6
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57446644"
---
# <a name="manage-ad-fs-trust-with-azure-ad-using-azure-ad-connect"></a>Gerir a confiança do AD FS com o Azure AD com o Azure AD Connect

## <a name="overview"></a>Descrição geral

O Azure AD Connect pode gerir a Federação entre locais Active Directory Federation Service (AD FS) e o Azure AD. Este artigo fornece uma visão geral de:

* Várias definições configuradas na confiança pelo Azure AD Connect
* As regras de transformação de emissão (regras de afirmação) definidas pelo Azure AD Connect
* Como cópia de segurança e restaurar a sua declaração de regras entre as atualizações e configuração de atualizações. 

## <a name="settings-controlled-by-azure-ad-connect"></a>Definições controladas pelo Azure AD Connect

O Azure AD Connect gerencia **apenas** as definições relacionadas com a confiança do Azure AD. O Azure AD Connect não modificar todas as definições em outras confianças da entidade confiadora no AD FS. A tabela seguinte indica as definições que são controladas pelo Azure AD Connect.

| Definição | Descrição |
| :--- | :--- |
| Certificado de assinatura de token | O Azure AD Connect pode servir-se para repor e recriar a confiança com o Azure AD. O Azure AD Connect não um rollover de imediato única de certificados de assinatura de tokens do AD FS e atualiza as definições de Federação do domínio do Azure AD.|
| Algoritmo de assinatura de token | A Microsoft recomenda utilizar SHA-256 como o algoritmo de assinatura de tokens. O Azure AD Connect pode detectar se o algoritmo de assinatura de token é definido como um valor menos seguro do que o SHA-256. Ele irá atualizar a definição para SHA-256 na próxima operação de configuração possíveis. Outra fidedignidade de entidade confiadora tem de ser atualizada para utilizar o novo certificado de assinatura de token. |
| Identificador de fidedignidade do Azure AD | O Azure AD Connect define o valor de identificador correto para a fidedignidade do Azure AD. AD FS identifica exclusivamente a confiança do Azure AD com o valor do identificador. |
| Pontos finais de AD do Azure | O Azure AD Connect certifica-se de que os pontos finais configurados para a fidedignidade do Azure AD são sempre de acordo com os valores recomendados mais recente para resiliência e desempenho. |
| Regras de transformação de emissão | Há um número de regras de afirmação que são necessários para otimizar o desempenho dos recursos do Azure AD numa configuração federada. O Azure AD Connect certifica-se de que a fidedignidade do Azure AD é sempre configurada com o conjunto certo de regras de afirmação recomendada. |
| Alternate-id | Se a sincronização estiver configurada para utilizar o id alternativo, o Azure AD Connect configura o AD FS para efetuar a autenticação com o id alternativo. |
| Atualização automática de metadados | Confiança com o Azure AD está configurada para atualização automática de metadados. Periodicamente, o AD FS verifica os metadados de confiança do Azure AD e mantém atualizado no caso de ele altera no lado do Azure AD. |
| Autenticação integrada do Windows (IWA) | Durante a operação de associação do Azure AD híbrido, IWA está ativada para o registo de dispositivos facilitar a associação do Azure AD híbrido para dispositivos de nível inferior |

## <a name="execution-flows-and-federation-settings-configured-by-azure-ad-connect"></a>Fluxos de execução e as definições de Federação configuradas pelo Azure AD Connect

Do Azure AD connect não atualiza todas as definições de confiança do Azure AD durante a configuração de fluxos. As definições modificadas dependem de qual fluxo de tarefas ou de execução está a ser executado. A tabela seguinte lista as definições de impacto nos fluxos de execução diferente.

| Fluxo de execução | Definições de afetados |
| :--- | :--- |
| Primeira instalação de passagem (rápida) | Nenhuma |
| Primeiro, de passar a instalação (novo farm do AD FS) | É criado um novo farm do AD FS e é criada uma confiança com o Azure AD a partir do zero. |
| Primeiro, de passar a instalação (farm do AD FS, existente de confiança do Azure AD) | Identificador de fidedignidade do Azure AD, as regras de transformação de emissão, pontos de extremidade do Azure AD, alternativa-id (se necessário), atualização automática de metadados |
| Redefina a confiança do Azure AD | Token de certificado de assinatura, assinatura algoritmo identificador de fidedignidade do Azure AD, de transformação de emissão de tokens de regras, pontos de extremidade do Azure AD, alternativa-id (se necessário), atualização automática de metadados |
| Adicionar servidor de federação | Nenhuma |
| Adicionar servidor WAP | Nenhuma |
| Opções do dispositivo | Regras de transformação de emissão, IWA para registo de dispositivos |
| Adicionar domínio federado | Se o domínio está a ser adicionado pela primeira vez, ou seja, a configuração está mudando da Federação único domínio para Federação de vários domínio – Azure AD Connect irá recriar a confiança do zero. Se a confiança com o Azure AD já está configurada para vários domínios, regras de transformação de emissão únicas são modificadas |
| Atualização SSL | Nenhuma |

Durante a todas as operações, na qual, qualquer definição é modificado, o Azure AD Connect faz com que uma cópia de segurança das definições de confiança atuais em **%ProgramData%\AADConnect\ADFS**

![Azure AD Connect mensagem de que mostra de página sobre a cópia de segurança de confiança do Azure AD existente](./media/how-to-connect-azure-ad-trust/backup2.png)

> [!NOTE]
> Antes da versão 1.1.873.0, a cópia de segurança consistiu em regras de transformação de emissão única e eles foram cópias de segurança no ficheiro de registo de rastreio de assistente.

## <a name="issuance-transform-rules-set-by-azure-ad-connect"></a>Regras de transformação de emissão definidas pelo Azure AD Connect

O Azure AD Connect certifica-se de que a fidedignidade do Azure AD é sempre configurada com o conjunto certo de regras de afirmação recomendada. A Microsoft recomenda a utilização do Azure AD connect para gerir a confiança do Azure AD. Esta secção lista o conjunto de regras de transformação de emissão e a respetiva descrição.

| Nome da regra | Descrição |
| --- | --- |
| Problema UPN | Esta regra de consulta o valor de userprincipalname como o atributo configurado nas definições de sincronização para userprincipalname.|
| Consultar o objectguid e msdsconsistencyguid para a afirmação de ImmutableId personalizada | Esta regra adiciona um valor temporário no pipeline para objectguid e um valor de msdsconsistencyguid se existir |
| Verifique a existência de msdsconsistencyguid | Com base em se o valor para msdsconsistencyguid já existe ou não, vamos definir um sinalizador temporário para direcionar o que usar como ImmutableId |
| Emitir msdsconsistencyguid como ID imutável, se existir | Emitir msdsconsistencyguid como ImmutableId se o valor existe |
| Emitir objectGuidRule se msdsConsistencyGuid regra não existe | Se o valor para msdsconsistencyguid não existir, o valor objectguid será emitido como ImmutableId |
| Emitir nameidentifier | Esta regra emite o valor para a afirmação nameidentifier.|
| Emitir accounttype para computadores associados a um domínio | Se a entidade a ser autenticada é um dispositivo associado ao domínio, esta regra emite o tipo de conta como DJ fazendo referência um dispositivo associado ao domínio |
| AccountType de problema com o valor de utilizador quando não é uma conta de computador | Se a entidade a ser autenticada um utilizador, esta regra emite o tipo de conta como utilizador |
| Emitir issuerid quando não é uma conta de computador | Esta regra emite o valor de issuerId quando a entidade de autenticação não é um dispositivo. O valor é criado através de um regex, que é configurado pelo Azure AD Connect. Regex é criado depois de levar em conta todos os domínios federados com o Azure AD Connect. |
| Emitir issuerid para autenticação de computador do DJ | Esta regra emite o valor de issuerId quando a entidade de autenticação é um dispositivo |
| Emitir onpremobjectguid para computadores associados a um domínio | Se a entidade a ser autenticada é um dispositivo associado ao domínio, esta regra emite o objectguid no local para o dispositivo |
| Passar o SID primário | Esta regra emite o SID principal da entidade de autenticação |
| Pass-through afirmação - insideCorporateNetwork | Este problemas de regra uma afirmação que ajuda a do Azure AD saber se a autenticação vem de dentro da rede empresarial ou externamente |
| Pass-Through afirmação – Psso |   |
| Emitir afirmações de expiração de palavra-passe | Este três de problemas de regra de afirmações para a hora de expiração de palavra-passe, o número de dias para a palavra-passe a expirar da entidade a ser autenticada e o URL onde roteá para alterar a palavra-passe.|
| Pass-through afirmação – authnmethodsreferences | O valor na afirmação emitido sob esta regra indica que tipo de autenticação foi efetuado para a entidade |
| Pass-through afirmação - multifactorauthenticationinstant | O valor desta afirmação Especifica o tempo, em UTC, quando o utilizador efetuou última autenticação de vários fatores. |
| Pass-through afirmação - AlternateLoginID | Esta regra emite a afirmação AlternateLoginID, se a autenticação foi realizada com o ID de início de sessão alternativo. |

> [!NOTE]
> As regras de afirmação de UPN do problema e ImmutableId serão diferentes se usar a opção de não-padrão durante a configuração do Azure AD Connect

## <a name="restore-issuance-transform-rules"></a>Restaurar as regras de transformação de emissão

A versão do Azure AD Connect 1.1.873.0 ou posterior faz uma cópia de segurança do Azure AD configurações de confiança sempre que uma atualização é feita para as definições de fidedignidade do Azure AD. As definições de fidedignidade do Azure AD são uma cópia de segurança **%ProgramData%\AADConnect\ADFS**. O nome do ficheiro está no formato seguinte AadTrust -&lt;data&gt;-&lt;tempo&gt;. txt, por exemplo, - AadTrust-20180710-150216.txt

![Uma captura de ecrã do exemplo de cópia de segurança de confiança do Azure AD](./media/how-to-connect-azure-ad-trust/backup.png)

Pode restaurar as regras de transformação de emissão utilizando os passos sugeridos abaixo

1. Abra a IU de gestão do AD FS no Gestor de servidores
2. Abra as propriedades de fidedignidade do Azure AD da **do AD FS &gt; da entidade Confiadora confianças &gt; plataforma de identidade do Microsoft Office 365 &gt; Editar política de emissão de afirmações**
3. Clique em **Adicionar regra**
4. No modelo de regra de afirmação, selecione Enviar afirmações utilizando uma regra personalizada e clique em **seguinte**
5. Copie o nome da regra de afirmação de arquivo de cópia de segurança e cole-o no campo **nome da regra de afirmação**
6. Copie a regra de afirmação de arquivo de cópia de segurança no campo de texto para **regra personalizada** e clique em **concluir**

> [!NOTE]
> Certifique-se de que suas regras adicionais não entram em conflito com as regras configuradas pelo Azure AD Connect.

## <a name="next-steps"></a>Passos Seguintes
* [Gerir e personalizar os serviços de Federação do Active Directory com o Azure AD Connect](how-to-connect-fed-management.md)
