---
title: Azure Multi-Factor Auth Providers - Azure Ative Directory
description: Quando deve utilizar um Fornecedor Auth com Azure MFA?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c8454d2ca83d4f406149e7eb73feb19ce59554f
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744130"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Quando utilizar um Fornecedor de Autenticação Multi-Factor Azure

> [!IMPORTANT]
> A partir de 1 de setembro de 2018, novos fornecedores de auth já não podem ser criados. Os fornecedores de auth existentes podem continuar a ser utilizados e atualizados, mas a migração já não é possível. A autenticação multi-factor continuará a estar disponível como recurso nas licenças Azure AD Premium.

A verificação em duas etapas está disponível por padrão para administradores globais que têm Azure Ative Directory e utilizadores microsoft 365. No entanto, se pretender tirar partido das [funcionalidades avançadas](howto-mfa-mfasettings.md), deverá comprar a versão completa do Multi-Factor Authentication (MFA) do Azure.

Um Azure Multi-Factor Auth Provider é utilizado para tirar partido das funcionalidades fornecidas pela Azure Multi-Factor Authentication para utilizadores que **não possuam licenças.**

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Ressalvas relacionadas com o Azure MFA SDK

Note que o SDK foi prectado e só vai continuar a trabalhar até 14 de novembro de 2018. Após esse tempo, as chamadas para o SDK irão falhar.

## <a name="what-is-an-mfa-provider"></a>O que é um Fornecedor de MFA?

Existem dois tipos de fornecedores Auth, e a distinção é em torno de como a sua assinatura Azure é cobrada. A opção por autenticação calcula o número de autenticações executadas num mês relativamente ao seu inquilino. Esta é a melhor opção no caso de existirem vários utilizadores que são ocasionalmente autenticados. A opção por utilizador calcula o número de indivíduos no seu inquilino que executam a verificação de dois passos num mês. Esta é a melhor opção caso existam alguns utilizadores com licenças, mas tem de alargar a MFA a mais utilizadores para além dos limites da sua licença.

## <a name="manage-your-mfa-provider"></a>Gerir o Fornecedor de MFA

Não pode alterar o modelo de utilização (por utilizador ativado ou por autenticação) após a criação de um fornecedor do MFA.

Se adquiriu licenças suficientes para cobrir todos os utilizadores que estão habilitados para MFA, pode eliminar completamente o fornecedor de MFA.

Se o fornecedor do MFA não estiver associado a um inquilino do Azure AD ou associar o novo fornecedor do MFA a um inquilino diferente do Azure AD, as definições do utilizador e as opções de configuração não são transferidas. Além disso, os servidores Azure MFA existentes precisam de ser reativados utilizando credenciais de ativação geradas através do Provedor de MFA.

### <a name="removing-an-authentication-provider"></a>Remoção de um fornecedor de autenticação

> [!CAUTION]
> Não há confirmação ao eliminar um fornecedor de autenticação. Selecionar **Delete** é um processo permanente.

Os fornecedores de autenticação podem ser encontrados no **portal Azure**  >  **Ative Directory**  >  **Security**  >  **MFA**  >  **Providers**. Clique em fornecedores listados para ver detalhes e configurações associados a esse fornecedor.

Antes de remover um fornecedor de autenticação, tome nota de quaisquer configurações personalizadas configuradas no seu fornecedor. Decida quais as definições que precisam de ser migradas para as definições gerais de MFA do seu fornecedor e complete a migração dessas definições. 

Os servidores Azure MFA ligados a fornecedores terão de ser reativados utilizando credenciais geradas pelas definições do **portal**  >  **Azure Ative Directory**  >  **Security**  >  **Security MFA**  >  **Server**. Antes de reativar, os seguintes ficheiros devem ser eliminados do `\Program Files\Multi-Factor Authentication Server\Data\` diretório nos Servidores Azure MFA no seu ambiente:

- caCert
- cert
- groupCACert
- groupKey
- nome de grupoName
- licenseKey
- pkey

![Eliminar um fornecedor de auth do portal Azure](./media/concept-mfa-authprovider/authentication-provider-removal.png)

Quando tiver confirmado que todas as definições foram migradas, pode navegar até ao **portal**  >  **Azure Ative Directory**  >  **Security**  >  **MFA**  >  **Providers** e selecionar as elipses **...** e selecionar **Delete**.

> [!WARNING]
> A eliminação de um fornecedor de autenticação eliminará qualquer informação de reporte associada a esse fornecedor. É melhor guardar relatórios de atividade antes de eliminar o seu fornecedor.

> [!NOTE]
> Os utilizadores com versões mais antigas da aplicação Microsoft Authenticator e do Azure MFA Server poderão ter de voltar a registar a sua aplicação.

## <a name="next-steps"></a>Passos seguintes

[Configurar as Definições do Multi-Factor Authentication](howto-mfa-mfasettings.md)
