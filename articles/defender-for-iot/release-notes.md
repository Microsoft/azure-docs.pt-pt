---
title: Novidades no Azure Defender para ioT
description: Este artigo permite-lhe saber quais as novidades no mais recente lançamento do Defender for IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2021
ms.author: shhazam
ms.openlocfilehash: 0281ebcdf1da27513e9b9256b508d911ec7697b5
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937723"
---
# <a name="whats-new"></a>Novidades

O Defender para IoT 10.0 fornece melhorias de recursos que melhoram a segurança, a gestão e a usabilidade.

## <a name="security"></a>Segurança

Foram feitas melhorias na recuperação de certificados e passwords para esta versão.

### <a name="certificates"></a>Certificados
  
Esta versão permite-lhe:

- Faça o upload dos certificados SSL diretamente para os sensores e consolas de gestão no local.
- Execute a validação entre a consola de gestão no local e os sensores conectados, e entre uma consola de gestão e uma consola de gestão de Alta Disponibilidade. A validação baseia-se em datas de validade, autenticidade de CA de raiz e listas de revogação de certificados.  Se a validação falhar, a sessão não continuará.

Para upgrades:

- Não há alteração no certificado SSL ou na funcionalidade de validação durante a atualização.
- Após a atualização, os utilizadores administrativos de consolas de gestão de sensores e no local podem substituir certificados SSL ou ativar a validação de certificadoS SSL a partir da janela 'Definições do Sistema' SSL.  

Para instalações frescas:

- Durante o início de sessão, os utilizadores são obrigados a utilizar um Certificado SSL (recomendado) ou um certificado auto-assinado gerado localmente (não recomendado)
- A validação do certificado é ligada por defeito para instalações frescas.

### <a name="password-recovery"></a>Recuperação de senha
  
Sensor e consola de gestão no local Os utilizadores administrativos podem agora recuperar senhas do Azure Defender para o portal IoT. Anteriormente, a recuperação da palavra-passe exigia a intervenção da equipa de apoio.

## <a name="onboarding"></a>Inclusão

### <a name="on-premises-management-console---committed-devices"></a>Consola de gestão no local - dispositivos comprometidos

Após o primeiro sôs-in na consola de gestão no local, os utilizadores passam a ser obrigados a carregar um ficheiro de ativação. O ficheiro contém o número agregado de dispositivos a serem monitorizados na rede organizacional. Este número é referido como o número de dispositivos comprometidos.
Os dispositivos comprometidos são definidos durante o processo de embarque no portal Azure Defender para IoT, onde o ficheiro de ativação é gerado.
Os utilizadores e a atualização da primeira vez são obrigados a carregar o ficheiro de ativação pela primeira vez.
Após a ativação inicial, o número de dispositivos detetados na rede pode exceder o número de dispositivos comprometidos. Este evento pode acontecer, por exemplo, se ligar mais sensores à consola de gestão. Se houver uma discrepância entre o número de dispositivos detetados e o número de dispositivos comprometidos, aparece um aviso na consola de gestão. Se este evento ocorrer, deverá carregar um novo ficheiro de ativação.

### <a name="pricing-page-options"></a>Opções de página de preços

A página de preços permite-lhe embarcar novas subscrições do Azure Defender para IoT e definir dispositivos comprometidos na sua rede.  
Além disso, a página de Preços permite-lhe agora gerir as subscrições existentes associadas a um compromisso de sensor e dispositivo de atualização.

### <a name="view-and-manage-onboarded-sensors"></a>Ver e gerir sensores a bordo

Uma nova página do portal site e sensores permite-lhe:

- Adicione informações descritivas sobre o sensor. Por exemplo, uma zona associada ao sensor ou etiquetas de texto livre.
- Ver e filtrar informações do sensor. Por exemplo, ver detalhes sobre sensores que estão ligados à nuvem ou geridos localmente ou ver informações sobre sensores numa zona específica.  

## <a name="usability"></a>Usabilidade

### <a name="azure-sentinel-new-connector-page"></a>Página de conector Azure Sentinel

A página de conector de dados Azure Defender para IoT em Azure Sentinel foi redesenhada. O conector de dados baseia-se agora em subscrições e não em IoT Hubs; permitindo aos clientes gerir melhor a sua ligação de configuração ao Azure Sentinel.

### <a name="azure-portal-permission-updates"></a>Atualizações de permissão do portal Azure  

O suporte do Leitor de Segurança e do Administrador de Segurança foi adicionado.

## <a name="other-updates"></a>Outras atualizações

### <a name="access-group---zone-permissions"></a>Grupo de acesso - permissões de zona
  
As regras do Grupo de Acesso às consolas de gestão no local não incluirão a opção de conceder acesso a uma zona específica. Não há qualquer alteração na definição de regras que utilizem sites, regiões e unidades de negócio.   Após a atualização, os Grupos de Acesso que continham regras que permitissem o acesso a zonas específicas serão modificados para permitir o acesso ao seu site principal, incluindo todas as suas zonas.

### <a name="terminology-changes"></a>Alterações de terminologia

O termo ativo foi renomeado dispositivo na consola de gestão de sensores e no local, relatórios e outras interfaces de solução.
Nos alertas de consola de gestão de sensores e no local, o termo Gerir este Evento foi nomeado Passos de Remediação.

## <a name="next-steps"></a>Passos seguintes

[Começar com o Defender para o IoT](getting-started.md)
