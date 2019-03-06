---
title: Como gerir dispositivos através do portal do Azure | Documentos da Microsoft
description: Saiba como utilizar o portal do Azure para gerir dispositivos.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2018
ms.author: markvi
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd57b753a4e59f2fb9590f081345d7af0de9124d
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57451474"
---
# <a name="how-to-manage-devices-using-the-azure-portal"></a>Como gerir dispositivos através do portal do Azure


Com a gestão de dispositivos no Azure Active Directory (Azure AD), pode assegurar que os seus utilizadores acedem aos seus recursos a partir de dispositivos que cumpram as suas normas de segurança e conformidade. 

Este artigo:

- Parte do princípio de que está familiarizado com o [introdução à gestão de dispositivos no Azure Active Directory](overview.md)

- Fornece informações sobre como gerir os seus dispositivos com o portal do Azure

## <a name="manage-devices"></a>Gerir dispositivos 

O portal do Azure fornece um local central para gerir os seus dispositivos. Pode obter para este local usando um [ligação direta](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) ou ao seguir estes passos manuais:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) como administrador.

2. Na barra de navegação esquerda, clique em **do Active Directory**.

    ![Configurar definições do dispositivo](./media/device-management-azure-portal/01.png)

3. Na **Manage** secção, clique em **dispositivos**.

    ![Configurar definições do dispositivo](./media/device-management-azure-portal/74.png)
 
O **dispositivos** página permite-lhe:

- Configurar as definições de gestão de dispositivos

- Localizar dispositivos

- Executar tarefas de gestão de dispositivos

- Reveja a gestão de dispositivos relacionados com registos de auditoria  
  

## <a name="configure-device-settings"></a>Configurar definições do dispositivo

Para gerir os seus dispositivos com o portal do Azure, tem de ser um de seus dispositivos [registados ou associados ao](overview.md#getting-devices-under-the-control-of-azure-ad) para o Azure AD. Como administrador, pode ajustar o processo de registro e associar dispositivos ao configurar as definições do dispositivo. 

![Configurar definições do dispositivo](./media/device-management-azure-portal/22.png)

A página de definições de dispositivo permite-lhe configurar:

![Gerir um dispositivo do Intune](./media/device-management-azure-portal/21.png)


- **Os utilizadores podem associar dispositivos ao Azure AD** -esta definição permite-lhe selecionar os utilizadores que podem [associar dispositivos](overview.md#azure-ad-joined-devices) para o Azure AD. A predefinição é **todos os**. Esta definição só é aplicável a associação do Azure AD no Windows 10.

- **Dispositivos associados de administradores locais adicionais no Azure AD** -pode selecionar os utilizadores que são concedidos direitos de administrador local num dispositivo. Utilizadores adicionados aqui são adicionados para o *administradores de dispositivos* função no Azure AD. Os administradores globais no Azure AD e os proprietários do dispositivo são concedidos direitos de administrador local por predefinição. Esta opção é um recurso de edição premium disponível por meio de produtos como o Azure AD Premium ou Enterprise Mobility Suite (EMS). 

- **Os utilizadores podem registar os seus dispositivos com o Azure AD** -tem de configurar esta definição para permitir que os dispositivos sejam [registado](overview.md#azure-ad-registered-devices) com o Azure AD. Se selecionou **None**, dispositivos não têm permissão para registar quando não estão associados ao Azure AD ou Azure AD híbrido. Inscrição com o Microsoft Intune ou gestão de dispositivos móveis (MDM) para o Office 365 requer o registo. Se tiver configurado qualquer um destes serviços, **todos os** está selecionado e **NONE** não está disponível.

- **Exigir multi-Factor Auth associar dispositivos** -pode escolher se os utilizadores têm de fornecer um segundo fator de autenticação para [associação](overview.md#azure-ad-joined-devices) respetivo dispositivo para o Azure AD. A predefinição é **não**. Recomendamos que requerem a autenticação multifator ao registar um dispositivo. Antes de ativar a autenticação multifator para este serviço, certifique-se de que a autenticação multifator está configurada para os utilizadores que registem os seus dispositivos. Para obter mais informações sobre serviços diferentes multi-factor authentication, consulte [introdução ao multi-factor authentication](../authentication/concept-mfa-whichversion.md). Esta definição não afeta a associação ao híbrido para o Windows 10 ou Windows 7. Isto só é aplicável a associação do Azure AD no registo de dispositivos Windows 10 e BYO para Windows 10, iOS e Android. 

- **Número máximo de dispositivos** -esta definição permite-lhe selecionar o número máximo de dispositivos que um utilizador pode ter no Azure AD. Se um utilizador atingir esta quota, eles não são conseguir adicionar dispositivos adicionais até que um ou mais dos dispositivos existentes são removidas. A citação de dispositivo é contada para todos os dispositivos que estão associados ao Azure AD ou do Azure AD, registadas-se hoje. O valor predefinido é **20**.

- **Os utilizadores podem sincronizar definições e dados de aplicação em todos os dispositivos** -por predefinição, esta definição está definida como **NONE**. A seleção de utilizadores específicos ou grupos ou todos permite ao utilizador as definições e dados de aplicação sincronizar entre os dispositivos Windows 10. Saiba mais sobre como funciona a sincronização no Windows 10.
Esta opção é uma capacidade de premium disponível por meio de produtos como o Azure AD Premium ou Enterprise Mobility Suite (EMS).
 




## <a name="locate-devices"></a>Localizar dispositivos

Tem duas opções para localizar dispositivos registados e associado ao:

- **Todos os dispositivos** no **gerir** seção o **dispositivos** página  

    ![Todos os dispositivos](./media/device-management-azure-portal/41.png)


- **Dispositivos** no **Manage** secção de um **utilizador** página
 
    ![Todos os dispositivos](./media/device-management-azure-portal/43.png)



Com as duas opções, pode obter para uma vista que:


- Permite que pesquise para dispositivos com o nome a apresentar como filtro.

- Fornece uma descrição detalhada dos dispositivos registados e associado ao

- Permite-lhe executar tarefas comuns de gestão de dispositivos
   

![Todos os dispositivos](./media/device-management-azure-portal/51.png)

Para alguns dispositivos iOS, os nomes de dispositivo que contém apóstrofos potencialmente podem utilizar caracteres diferentes que se pareçam com apóstrofos. Procurar por esses dispositivos é um pouco complicado - se não vir os resultados da pesquisa corretamente, certifique-se que a cadeia de procura contém apóstrofe correspondente.

## <a name="device-management-tasks"></a>Tarefas de gestão de dispositivos

Como administrador global, pode gerir os dispositivos registados ou associados. Os administradores de serviço do Intune podem:
 
- Dispositivos de atualização - exemplos são as operações diárias, tais como a ativação/desativação de dispositivos

- Eliminar dispositivos – quando um dispositivo é extinto e deve ser eliminado no Azure AD

Esta secção fornece informações sobre tarefas de gestão de dispositivos comuns.



### <a name="manage-an-intune-device"></a>Gerir um dispositivo do Intune

Se for um administrador do Intune, pode gerir dispositivos marcados como **Microsoft Intune**. 

![Gerir um dispositivo do Intune](./media/device-management-azure-portal/31.png)


### <a name="enable--disable-an-azure-ad-device"></a>Ativar / desativar um dispositivo do Azure AD

Para ativar / desativar um dispositivo, tem duas opções:

- O menu de tarefas ("...") na **todos os dispositivos** página

    ![Gerir um dispositivo do Intune](./media/device-management-azure-portal/71.png)

- A barra de ferramentas sobre o **dispositivos** página

    ![Gerir um dispositivo do Intune](./media/device-management-azure-portal/32.png)


**Observações:**

- Tem de ser um administrador global no Azure AD para ativar / desativar um dispositivo. 
- Desativar um dispositivo impede que um dispositivo com êxito a autenticação com o Azure AD, impedindo que o dispositivo acedam os recursos do Azure AD que estão protegidos pelo dispositivo AC ou com as suas credenciais de WH4B. 



### <a name="delete-an-azure-ad-device"></a>Eliminar um dispositivo do Azure AD

Para eliminar um dispositivo, tem duas opções:

- O menu de tarefas ("...") na **todos os dispositivos** página

    ![Gerir um dispositivo do Intune](./media/device-management-azure-portal/72.png)

- A barra de ferramentas sobre o **dispositivos** página

    ![Eliminar um dispositivo](./media/device-management-azure-portal/34.png)


**Observações:**

- Tem de ser um administrador global ou administrador do Intune no Azure AD para eliminar um dispositivo.

- A eliminar um dispositivo:
 
    - Impede que um dispositivo acedem aos seus recursos do Azure AD. 

    - Remove todos os detalhes que estão ligados ao dispositivo, por exemplo, chaves do BitLocker para dispositivos Windows.  

    - Representa uma atividade não recuperável e não é recomendada a menos que seja necessário.

Se um dispositivo for gerido por outra autoridade de gestão (por exemplo, o Microsoft Intune), certifique-se de que o dispositivo foi apagado / extinto antes de eliminar o dispositivo no Azure AD.

 


### <a name="view-or-copy-device-id"></a>Ver ou copiar o ID de dispositivo

Pode utilizar um ID de dispositivo para verificar os detalhes de ID de dispositivo no dispositivo ou com o PowerShell durante a resolução de problemas. Para acessar a opção de cópia, clique no dispositivo.

![Ver um ID de dispositivo](./media/device-management-azure-portal/35.png)
  

### <a name="view-or-copy-bitlocker-keys"></a>Ver ou copiar chaves do BitLocker

Pode ver e copiar as chaves do BitLocker para ajudar os utilizadores recuperem a unidade encriptada. Essas chaves só estão disponíveis para dispositivos Windows que são encriptados e tem as chaves armazenadas no Azure AD. Pode copiar estas chaves ao aceder a detalhes do dispositivo.
 
![Exibir as chaves do BitLocker](./media/device-management-azure-portal/36.png)

Para ver ou copiar as chaves do BitLocker, terá de ser o proprietário do dispositivo, ou um utilizador que tenha, pelo menos, uma das seguintes funções atribuídas:

- Administradores globais
- Administradores do suporte técnico
- Administradores de segurança
- Leitores de segurança
- Administradores de serviço do Intune

> [!NOTE]
> Azure AD híbrido associado a um Windows 10 dispositivos não têm um proprietário. Então, se estiver à procura de um dispositivo pelo proprietário e não encontrá-lo, procure pelo ID de dispositivo.


## <a name="audit-logs"></a>Registos de auditoria


Atividades de dispositivo estão disponíveis por meio dos registos de atividades. Isto inclui atividades acionadas pelo serviço de registo do dispositivo e pelos utilizadores:

- Criação do dispositivo e adicionar proprietários / utilizadores do dispositivo

- Alterações às definições do dispositivo

- Operações de dispositivo, como a exclusão ou atualização de um dispositivo
 
É o ponto de entrada para os dados de auditoria **registos de auditoria** no **atividade** seção o **dispositivos** página.

![Registos de auditoria](./media/device-management-azure-portal/61.png)


Um registo de auditoria tem uma vista de lista predefinida que mostra:

- A data e hora da ocorrência

- Os destinos

- O iniciador / ator (quem) de uma atividade

- A atividade (o que)

![Registos de auditoria](./media/device-management-azure-portal/63.png)

Pode personalizar a vista de lista ao clicar em **Colunas** na barra de ferramentas.
 
![Registos de auditoria](./media/device-management-azure-portal/64.png)


Para limitar os dados comunicados a um nível que funcione para si, pode filtrar os dados de auditoria através dos seguintes campos:

- Categoria
- Tipo de recurso de atividade
- Atividade
- Intervalo de datas
- Destino
- Iniciado por (Ator)

Além dos filtros, pode procurar entradas específicas.

![Registos de auditoria](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Passos Seguintes

* [Introduction to device management in Azure Active Directory](overview.md) (Introdução à gestão de dispositivos no Azure Active Directory)



