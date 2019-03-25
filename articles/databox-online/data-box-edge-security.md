---
title: Segurança de borda de caixa de dados | Documentos da Microsoft
description: Descreve as funcionalidades de segurança e privacidade que protegem o seu dispositivo Edge de caixa de dados, o serviço e a dados no local e na cloud.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: 43de22f7e56178559df4fc45980d064962580d2b
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403396"
---
# <a name="data-box-edge-security-and-data-protection"></a>Segurança de borda de caixa de dados e proteção de dados

A segurança é uma preocupação importante quando uma nova tecnologia, especialmente se a tecnologia é utilizada com dados confidenciais ou proprietários. Solução de ponta de caixa de dados do Microsoft Azure ajuda a garantir que apenas as entidades autorizadas podem ver, modificar ou eliminar os seus dados.

Este artigo descreve os recursos de segurança de borda de caixa de dados que ajudam a proteger todos os componentes da solução e os dados armazenados nos mesmos.

A solução de borda de caixa de dados do Azure consiste em quatro componentes principais que interagem entre si:

- **Edge de caixa de dados / serviço de Gateway de caixa de dados alojadas no Azure** – o recurso de gestão que utiliza para criar a ordem de dispositivo, configurar o dispositivo e, em seguida, controlar a ordem até à conclusão.
- **Dispositivo de limite de caixa de dados** – o dispositivo de transferência que é enviado para si para importar os dados no local para o Azure.
- **Os clientes/anfitriões ligados ao dispositivo** – os clientes na sua infraestrutura de que se ligam para o dispositivo de limite de caixa de dados e contêm dados que precisam ser protegido.
- **Armazenamento na cloud** – A localização na cloud do Azure onde os dados são armazenados. Esta localização é, normalmente, a conta de armazenamento ligada para o recurso de borda de caixa de dados que criou.


## <a name="data-box-edgedata-box-gateway-service-protection"></a>Proteção de serviço de Gateway de caixa de Edge/dados de caixa de dados

O serviço de Gateway de caixa de Edge/dados de caixa de dados é um serviço de gestão alojado no Microsoft Azure. O serviço é utilizado para configurar e gerir o dispositivo.

- Acesso ao serviço de Gateway de caixa de Edge/dados de caixa de dados requer a sua organização tenha um Enterprise Agreement (EA) ou uma subscrição do fornecedor de soluções Cloud (CSP). Para obter mais informações, aceda a [Inscreva-se uma subscrição do Azure](https://azure.microsoft.com/resources/videos/sign-up-for-microsoft-azure/)!
- Uma vez que o serviço de gestão está alojado no Azure, é protegida pelos recursos de segurança do Azure. Para obter mais informações sobre as funcionalidades de segurança fornecidas pelo Microsoft Azure, aceda ao [Centro de Fidedignidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

## <a name="data-box-edge-device-protection"></a>Proteção de dispositivos do Edge de caixa de dados

O dispositivo de limite de caixa de dados é um dispositivo no local que ajuda a transformar os dados por processá-lo localmente e, em seguida, enviá-la para o Azure. O dispositivo:

- Precisa de uma chave de ativação para aceder ao serviço de Gateway de caixa de Edge/dados de caixa de dados.
- É protegida sempre por uma palavra-passe do dispositivo.
- É um dispositivo bloqueado. O dispositivo BMC e BIOS estiver protegido por palavra-passe com acesso de usuário limitado para o BIOS.
- Tem o arranque seguro ativado.
- Executa o Windows Defender o Device Guard. O Device Guard permite que execute apenas aplicações fidedignas que define nas suas políticas de integridade de código. 

### <a name="protect-the-device-via-activation-key"></a>Proteger o dispositivo através de chave de ativação

Apenas um dispositivo de limite de caixa de dados autorizado tem permissão para associar o serviço de Gateway de caixa de Edge/dados de caixa de dados que criou na sua subscrição do Azure. Para autorizar um dispositivo, tem de utilizar uma chave de ativação para ativar o dispositivo com o serviço de Gateway de caixa de Edge/dados de caixa de dados. Para obter mais informações, aceda a [obter uma chave de ativação](data-box-edge-deploy-prep.md#get-the-activation-key).

A chave de ativação que utiliza:

- É uma chave de autenticação com base do Azure Active Directory (AAD).
- Expira após três dias.
- Não é utilizado após a ativação do dispositivo.
 
Depois de um dispositivo é ativado, utiliza os tokens para comunicar com o Microsoft Azure.

### <a name="protect-the-device-via-password"></a>Proteger o dispositivo através de palavra-passe

As palavras-passe Certifique-se de que os dados estão acessíveis aos utilizadores autorizados apenas. Limite de caixa de dados e dados de caixa de Gateway dispositivos arranque no estado bloqueado.

Pode:

- Ligar à IU do dispositivo através de um browser da web local e, em seguida, forneça uma palavra-passe para iniciar sessão no dispositivo.
- Ligar remotamente à interface do PowerShell do dispositivo através de HTTP. Gestão remota está ativada por predefinição. Em seguida, pode fornecer a palavra-passe do dispositivo para iniciar sessão no dispositivo. Para obter mais informações, aceda a [ligar remotamente ao seu dispositivo Edge de caixa de dados](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

Mantenha as seguintes práticas recomendadas em mente:

- O serviço de dados caixa Edge não é possível obter palavras-passe existentes: ele só pode repô-los através do portal do Azure. Recomendamos que armazene todas as senhas num local seguro para que não é necessário repor uma palavra-passe, se ele é esquecido. Ao repor uma palavra-passe, certifique-se de que notificar todos os utilizadores antes de repor-lo.
- Utilização da interface do Usuário para o local da web [alterar a palavra-passe](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Se alterar a palavra-passe, certifique-se de que todos os utilizadores de acesso remoto de notificar para que eles não se deparam com um falha de início de sessão.
- Pode acessar a interface do Windows PowerShell do seu dispositivo remotamente através de HTTP. Como prática recomendada de segurança, deve usar HTTP apenas em redes fidedignas.
- Certifique-se de que as palavras-passe do dispositivo são fortes e bem protegida. Siga os [melhores práticas de palavra-passe](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices#enable-password-management).

## <a name="protect-the-data"></a>Proteger os dados

Esta secção descreve as funcionalidades de segurança de borda de caixa de dados que protegem os dados em trânsito e dados armazenados.

### <a name="protect-data-at-rest"></a>Proteger dados em repouso

Para os dados Inativos:

- Para os dados em repouso, dados de caixa de borda usa a criptografia de BitLocker XTS AES-256 para proteger os dados locais.
- Para os dados que residem nas partilhas, o acesso às partilhas está limitado.

    - Para clientes SMB que acedem os dados de partilha, necessitem de credenciais de utilizador associadas à partilha. Estas credenciais são definidas no momento da criação da partilha.
    - Para clientes de NFS aceder às partilhas, os endereços IP dos clientes tem de ser adicionados no momento da criação da partilha.


### <a name="protect-data-in-flight"></a>Proteger dados em trânsito

Para o data-em-voo:

- Para dados que se encontra entre o dispositivo e o Azure, o padrão TLS 1.2 é usado. Não há nenhum contingência para TLS 1.1 e versões anteriores. A comunicação do agente será bloqueada se o TLS 1.2 não é suportada. O TLS 1.2 também é necessário para o portal e operações de gestão do SDK.
- Quando os clientes acessam o seu dispositivo através da web local da interface do Usuário num navegador, o padrão TLS 1.2 é usado como o protocolo de segurança padrão.

    - A melhor prática é configurar o browser para utilizar TLS 1.2.
    - Se o navegador não oferece suporte a TLS 1.2, pode utilizar o TLS 1.1 ou TLS 1.0.
- Para proteger os dados quando copia a partir dos seus servidores de dados, recomendamos que utilize o SMB 3.0 com a encriptação.

### <a name="protect-data-via-storage-accounts"></a>Proteger dados através de contas de armazenamento

O dispositivo está associado uma conta de armazenamento que é utilizada como um destino para os seus dados no Azure. Acesso à conta de armazenamento é controlado por duas de 512 bits de armazenamento e a subscrição associadas essa conta de armazenamento de chaves de acesso.

Uma das chaves é utilizada para autenticação quando o dispositivo de limite de caixa de dados acede a conta de armazenamento. A outra chave é mantida em reserva, permitindo-lhe rodar as chaves periodicamente.

Por motivos de segurança, muitos data Centers requerem a rotação de chaves. Recomendamos que siga estas práticas recomendadas para a rotação de chaves:

- A chave da conta de armazenamento é semelhante à palavra-passe de raiz da conta de armazenamento. Proteger cuidadosamente a sua chave de conta. Não distribua a palavra-passe para outros utilizadores, duro codificá-la ou guardá-lo em qualquer lugar em texto não encriptado, que é acessível a outras pessoas.
- [Regenerar a chave da conta](../storage/common/storage-account-manage.md#regenerate-access-keys) através do portal do Azure se considerar poderão ter sido comprometida.
- Rodar e, em seguida [sincronizar as chaves de conta de armazenamento](data-box-gateway-manage-shares.md#sync-storage-keys) regularmente para ajudar a garantir que sua conta de armazenamento não é acessada por utilizadores não autorizados.
- Periodicamente, o administrador do Azure deve alterar ou voltar a gerar a chave primária ou secundária, utilizando a secção de armazenamento do portal do Azure para aceder diretamente à conta de armazenamento.


## <a name="manage-personal-information"></a>Gerir informações pessoais

O limite do Data Box / serviço de Gateway de caixa de dados recolhe as informações pessoais nas seguintes instâncias principais:

- **Detalhes da encomenda** – Depois de criada a encomenda, o endereço de envio, o e-mail e as informações de contacto dos utilizadores são armazenados no portal do Azure. As informações guardadas incluem:
  - Nome do contacto
  - Número de telefone
  - Email
  - Morada
  - Localidade
  - Código postal
  - Estado
  - País/Província/Região
  - Número de controlo de envio

    Os detalhes do pedido são encriptados e armazenados no serviço. O serviço mantém as informações até que elimine o recurso ou ordem explicitamente. Além disso, a eliminação de recursos e a ordem de correspondente está bloqueada desde o momento em que o dispositivo é enviado até que o dispositivo regressa ao Microsoft.

- **Endereço para remessa** – depois do pedido é feito, o serviço do Data Box fornece o endereço de envio para os operadores de terceiros, como no-BREAK.

- **Os utilizadores de partilhar** -os utilizadores no seu dispositivo também podem aceder os dados que residem nas partilhas de. Uma lista de utilizadores que podem aceder os dados de partilha é apresentada e pode ser visualizada. Esta lista também será eliminada quando as partilhas são eliminadas. Para ver a lista de utilizadores que podem aceder ou eliminar uma partilha, siga os passos em [gerir partilhas no limite da caixa de dados](data-box-gateway-manage-shares.md).

Para obter mais informações, reveja a política de privacidade da Microsoft no [Centro de Fidedignidade](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Passos Seguintes

[Implementar o dispositivo do Edge de caixa de dados](data-box-edge-deploy-prep.md).

