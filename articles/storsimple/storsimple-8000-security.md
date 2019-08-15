---
title: Segurança da série StorSimple 8000 | Microsoft Docs
description: Descreve os recursos de segurança e privacidade que protegem o serviço StorSimple, o dispositivo e os dados locais e na nuvem.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/18/2018
ms.author: alkohli
ms.openlocfilehash: 31c432b884ce92c508dd7f893c12ba13acff28e8
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963522"
---
# <a name="storsimple-security-and-data-protection"></a>Segurança e proteção de dados do StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Descrição geral

A segurança é uma grande preocupação para qualquer pessoa que esteja adotando uma nova tecnologia, especialmente quando a tecnologia for usada com dados confidenciais ou proprietários. Ao avaliar diferentes tecnologias, você deve considerar o aumento dos riscos e dos custos da proteção de dados. O Microsoft Azure StorSimple fornece uma solução de segurança e privacidade para proteção de dados, ajudando a garantir:

* **Confidencialidade** – somente entidades autorizadas podem exibir seus dados.
* **Integridade** – somente entidades autorizadas podem modificar ou excluir seus dados.

A solução Microsoft Azure StorSimple consiste em quatro componentes principais que interagem entre si:

* O **serviço storsimple Device Manager hospedado no Microsoft Azure** – o serviço de gerenciamento que você usa para configurar e provisionar o dispositivo StorSimple.
* **Dispositivo StorSimple** – um dispositivo físico instalado em seu datacenter. Todos os hosts e clientes que geram dados se conectam ao dispositivo StorSimple, e o dispositivo gerencia os dados e os move para a nuvem do Azure, conforme apropriado.
* **Clientes/hosts conectados ao dispositivo** – os clientes em sua infraestrutura que se conectam ao dispositivo StorSimple e geram dados que precisam ser protegidos.
* **Armazenamento na cloud** – A localização na cloud do Azure onde os dados são armazenados.

As seções a seguir descrevem os recursos de segurança do StorSimple que ajudam a proteger cada um desses componentes e os dados armazenados neles. Ele também inclui uma lista de perguntas que você pode ter sobre Microsoft Azure StorSimple segurança e as respostas correspondentes.

## <a name="storsimple-device-manager-service-protection"></a>Proteção do serviço StorSimple Device Manager

O serviço StorSimple Device Manager é um serviço de gerenciamento hospedado no Microsoft Azure e usado para gerenciar todos os dispositivos StorSimple que sua organização adquiriu. Você pode acessar o serviço de Device Manager do StorSimple usando suas credenciais organizacionais para fazer logon no portal do Azure por meio de um navegador da Web.

O acesso ao serviço de Device Manager do StorSimple exige que sua organização tenha uma assinatura do Azure que inclua o StorSimple. A subscrição controla as funcionalidades a que pode aceder no portal do Azure. Se sua organização não tiver uma assinatura do Azure e você quiser saber mais sobre elas, consulte inscrever-se [no Azure como uma organização](../active-directory/fundamentals/sign-up-organization.md).

Como o serviço StorSimple Device Manager é hospedado no Azure, ele é protegido pelos recursos de segurança do Azure. Para obter mais informações sobre as funcionalidades de segurança fornecidas pelo Microsoft Azure, aceda ao [Centro de Fidedignidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

## <a name="storsimple-device-protection"></a>Proteção do dispositivo StorSimple

O dispositivo StorSimple é um dispositivo de armazenamento híbrido local que contém unidades de estado sólido (SSDs) e unidades de disco rígido (HDDs), juntamente com controladores redundantes e recursos de failover automático. Os controladores gerenciam camadas de armazenamento, colocando dados atualmente usados (ou ativos) no armazenamento local (no dispositivo StorSimple ou servidores locais), enquanto movem os dados usados com menos frequência para a nuvem.

Somente dispositivos StorSimple autorizados têm permissão para ingressar no serviço StorSimple Device Manager que você criou em sua assinatura do Azure. Para autorizar um dispositivo, você deve registrá-lo com o serviço de Device Manager do StorSimple fornecendo a chave de registro do serviço. A chave de registro do serviço é uma chave aleatória de 128 bits gerada no portal do Azure.

![Chave de registro do serviço](./media/storsimple-security/ServiceRegistrationKey.png)

Para saber como obter uma chave de registro de serviço, [vá para a etapa 2: Obtenha a chave](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key)de registro do serviço.

A chave de registro do serviço é uma chave longa que contém mais de 100 caracteres. Você pode copiar a chave e salvá-la em um arquivo de texto em um local seguro para que você possa usá-la para autorizar dispositivos adicionais conforme necessário. Se a chave de registro do serviço for perdida depois que você registrar seu primeiro dispositivo, você poderá gerar uma nova chave do serviço StorSimple Device Manager. Isso não afetará a operação dos dispositivos existentes.

Depois que um dispositivo é registrado, ele usa tokens para se comunicar com Microsoft Azure. A chave de registro do serviço não é usada após o registro do dispositivo.

> [!NOTE]
> Recomendamos que você gere novamente a chave de registro do serviço depois de cada uso.


## <a name="protect-your-storsimple-solution-via-passwords"></a>Proteger sua solução StorSimple por meio de senhas

As senhas são um aspecto importante da segurança do computador e são usadas extensivamente na solução StorSimple para ajudar a garantir que seus dados estejam acessíveis somente a usuários autorizados. O StorSimple permite que você configure as seguintes senhas:

* Senha de administrador do dispositivo StorSimple
* Iniciador do Challenge Handshake Authentication Protocol (CHAP) e senhas de destino
* Palavra-passe do Snapshot Manager StorSimple

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Windows PowerShell para StorSimple e a senha de administrador do dispositivo StorSimple

Windows PowerShell para StorSimple é uma interface de linha de comando que você pode usar para gerenciar o dispositivo StorSimple. Windows PowerShell para StorSimple tem recursos que permitem registrar seu dispositivo, configurar a interface de rede em seu dispositivo, instalar determinados tipos de atualizações, solucionar problemas de seu dispositivo acessando a sessão de suporte e alterar o estado do dispositivo. Você pode acessar Windows PowerShell para StorSimple conectando-se ao console serial no dispositivo ou usando a comunicação remota do Windows PowerShell.

A comunicação remota do PowerShell pode ser feita via HTTPS ou HTTP. Se o gerenciamento remoto via HTTPS estiver habilitado, será necessário baixar o certificado de gerenciamento remoto do dispositivo e instalá-lo no cliente remoto. Para obter mais informações sobre a comunicação remota do PowerShell, acesse [conectar remotamente ao seu dispositivo StorSimple](storsimple-8000-remote-connect.md).

Depois de usar Windows PowerShell para StorSimple para se conectar ao dispositivo, será necessário fornecer a senha de administrador do dispositivo para fazer logon no dispositivo.

![Palavra-passe do administrador do dispositivo](./media/storsimple-security/DeviceAdminPW.png)

Tenha em mente as seguintes práticas recomendadas:

* O gerenciamento remoto é desativado por padrão. Você pode usar o serviço de Device Manager do StorSimple para habilitá-lo. Como prática recomendada de segurança, o acesso remoto deve ser habilitado somente durante o período de tempo que é realmente necessário.
* Se você alterar a senha, lembre-se de notificar todos os usuários de acesso remoto para que eles não tenham uma perda de conectividade inesperada.
* O serviço StorSimple Device Manager não pode recuperar senhas existentes: ela só pode redefini-las. É recomendável que você armazene todas as senhas em um local seguro para não precisar redefinir uma senha se ela for esquecida. Se você precisar redefinir uma senha, lembre-se de notificar todos os usuários antes de redefini-los.

Você pode acessar a interface do Windows PowerShell usando uma conexão serial para o dispositivo. Você também pode acessá-lo remotamente usando HTTP ou HTTPS, que fornece segurança adicional. O HTTPS fornece um nível mais alto de segurança do que uma conexão serial ou HTTP. No entanto, para usar HTTPS, você deve primeiro instalar um certificado no computador cliente que acessará o dispositivo. Você pode baixar o certificado de acesso remoto na página de configuração do dispositivo no serviço StorSimple Device Manager. Se o certificado para acesso remoto for perdido, você deverá baixar um novo certificado e propagá-lo para todos os clientes que estão autorizados a usar o gerenciamento remoto.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Iniciador do Challenge Handshake Authentication Protocol (CHAP) e senhas de destino

O CHAP é um esquema de autenticação usado pelo dispositivo StorSimple para validar a identidade de clientes remotos. A verificação é baseada em uma senha compartilhada. O CHAP pode ser unidirecional ou mútuo (bidirecional). Com o CHAP unidirecional, o destino (o dispositivo StorSimple) autentica um iniciador (host). O CHAP mútuo ou reverso requer que o destino autentique o iniciador e, em seguida, o iniciador autentique o destino. O StorSimple pode ser configurado para usar qualquer um dos métodos.

Lembre-se do seguinte ao configurar o CHAP:

* O nome de usuário do CHAP deve conter menos de 233 caracteres.
* A senha do CHAP deve ter entre 12 e 16 caracteres. A tentativa de usar um nome de usuário ou senha mais longo resultará em uma falha de autenticação no host do Windows.
* Você não pode usar a mesma senha para o iniciador CHAP e o destino CHAP.
* Depois de definir a senha, ela pode ser alterada, mas não pode ser recuperada. Se a senha for alterada, lembre-se de notificar todos os usuários de acesso remoto para que eles possam se conectar com êxito ao dispositivo StorSimple.

Para obter mais informações sobre o CHAP e como configurá-lo para sua solução StorSimple, vá para [Configurar o CHAP para seu dispositivo storsimple](storsimple-8000-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>Palavra-passe do Snapshot Manager StorSimple

O StorSimple Snapshot Manager é um snap-in do MMC (console de gerenciamento Microsoft) que usa grupos de volumes e o Serviço de Cópias de Sombra de Volume do Windows para gerar backups consistentes com o aplicativo. Além disso, você pode usar o StorSimple Snapshot Manager para criar agendamentos de backup e clonar ou restaurar volumes.

Ao configurar um dispositivo para usar o Snapshot Manager do StorSimple, você será solicitado a fornecer a senha de Snapshot Manager do StorSimple. Essa senha é definida pela primeira vez no Windows PowerShell para StorSimple durante o registro. A senha também pode ser definida e alterada no serviço StorSimple Device Manager. Essa senha autentica o dispositivo com o StorSimple Snapshot Manager.

![Palavra-passe do Snapshot Manager StorSimple](./media/storsimple-security/SnapshotMgrPassword.png)

A senha de Snapshot Manager do StorSimple deve ter de 14 a 15 caracteres e deve conter 3 ou mais de uma combinação de letras maiúsculas, letras minúsculas, números e caracteres especiais. Depois de definir a senha de Snapshot Manager do StorSimple, ela pode ser alterada, mas não pode ser recuperada. Se você alterar a senha, lembre-se de notificar todos os usuários remotos.

Para obter mais informações sobre o Snapshot Manager do StorSimple, acesse [o que é o storsimple snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Práticas recomendadas de senha

Recomendamos que você use as diretrizes a seguir para ajudar a garantir que as senhas do StorSimple sejam fortes e bem protegidas:

* Altere suas senhas a cada três meses. A alteração das senhas é imposta anualmente.
* Use senhas fortes. Para obter mais informações, vá para [criar senhas mais fortes e protegê-las](https://cloudblogs.microsoft.com/microsoftsecure/2014/08/25/create-stronger-passwords-and-protect-them/).
* Sempre usar senhas diferentes para mecanismos de acesso diferentes; cada uma das senhas que você especificar deve ser exclusiva.
* Não compartilhe senhas com ninguém que não esteja autorizado a acessar o dispositivo StorSimple.
* Não fale sobre uma senha na frente de outras pessoas ou dica no formato de uma senha.
* Se você suspeitar que uma conta ou senha foi comprometida, relate o incidente para seu departamento de segurança de informações.
* Trate todas as senhas como informações confidenciais. 

## <a name="storsimple-data-protection"></a>Proteção de dados do StorSimple

Esta seção descreve os recursos de segurança do StorSimple que protegem dados em trânsito e dados armazenados.

Conforme descrito em outras seções, as senhas são usadas para autorizar e autenticar os usuários antes que eles possam obter acesso à solução StorSimple. Outra consideração de segurança é proteger dados de usuários não autorizados enquanto eles estão sendo transferidos entre sistemas de armazenamento e enquanto estão sendo armazenados. As seções a seguir descrevem os recursos de proteção de dados fornecidos com o StorSimple.

> [!NOTE]
> A eliminação de duplicação fornece proteção adicional para os dados armazenados no dispositivo StorSimple e no armazenamento Microsoft Azure. Quando os dados têm eliminação de duplicação, os objetos de dados são armazenados separadamente dos metadados usados para mapeá-los e acessá-los: não há nenhum contexto de nível de armazenamento disponível para reconstruir os dados com base na estrutura de volume, sistema de arquivos ou nome de arquivo.


## <a name="protect-data-flowing-through-the-service"></a>Proteger dados que fluem pelo serviço

A principal finalidade do serviço de Device Manager do StorSimple é gerenciar e configurar o dispositivo StorSimple. O serviço StorSimple Device Manager é executado em Microsoft Azure. Você usa o portal do Azure para inserir dados de configuração do dispositivo e, em seguida, Microsoft Azure usa o serviço de Device Manager do StorSimple para enviar os dados para o dispositivo. O StorSimple usa um sistema de pares de chaves assimétricas para ajudar a garantir que um comprometimento do serviço do Azure não resulte em um comprometimento de informações armazenadas.

![Criptografia de dados em trânsito](./media/storsimple-security/DataEncryption.png)

O sistema de chave assimétrica ajuda a proteger os dados que fluem pelo serviço da seguinte maneira:

1. Um certificado de criptografia de dados que usa um par de chaves pública e privada assimétrica é gerado no dispositivo e é usado para proteger os dados. As chaves são geradas quando o primeiro dispositivo é registrado.
2. As chaves de certificado de criptografia de dados são exportadas para um arquivo de troca de informações pessoais (. pfx) que é protegido pela chave de criptografia de dados de serviço, que é uma chave forte de 128 bits que é gerada aleatoriamente pelo primeiro dispositivo durante o registro.
3. A chave pública do certificado é disponibilizada com segurança para o serviço de Device Manager do StorSimple e a chave privada permanece com o dispositivo.
4. Os dados que entram no serviço são criptografados usando a chave pública e descriptografados usando a chave privada armazenada no dispositivo, garantindo que o serviço do Azure não possa descriptografar o fluxo de dados para o dispositivo.

A chave de criptografia de dados de serviço é gerada somente no primeiro dispositivo registrado com o serviço. Todos os dispositivos subsequentes registrados com o serviço devem usar a mesma chave de criptografia de dados de serviço.

> [!IMPORTANT]
> É muito importante fazer uma cópia da chave de criptografia de dados de serviço e salvá-la em um local seguro. Uma cópia da chave de criptografia de dados de serviço deve ser armazenada de forma que possa ser acessada por uma pessoa autorizada e possa ser facilmente comunicada ao administrador do dispositivo.
> 
> Se a chave de criptografia de dados de serviço for perdida, uma pessoa de suporte da Microsoft poderá ajudá-lo a recuperá-la desde que você tenha pelo menos um dispositivo em um estado online. Recomendamos que você altere a chave de criptografia de dados de serviço depois que ela for recuperada.

Para alterar a chave de criptografia de dados de serviço e o certificado de criptografia de dados correspondente, siga as etapas em [alterar a chave de criptografia de dados de serviço para o serviço de Device Manager do StorSimple](storsimple-8000-manage-service.md#change-the-service-data-encryption-key). A alteração das chaves de criptografia exige que todos os dispositivos sejam atualizados com a nova chave. Portanto, recomendamos que você altere a chave quando todos os dispositivos estiverem online. Se os dispositivos estiverem offline, suas chaves poderão ser alteradas em um horário diferente. Os dispositivos com chaves desatualizadas ainda poderão executar backups, mas não poderão restaurar dados até que a chave seja atualizada.

A chave de criptografia de dados de serviço e o certificado de criptografia de dados não expiram. No entanto, recomendamos que você altere a chave de criptografia de dados de serviço anualmente para ajudar a impedir o comprometimento da chave.

## <a name="protect-data-at-rest"></a>Proteger dados em repouso

O dispositivo StorSimple gerencia os dados armazenando-os em camadas localmente e na nuvem, dependendo da frequência de uso. Todos os computadores host que estão conectados ao dispositivo enviam dados para o dispositivo, que, em seguida, move os dados para a nuvem, conforme apropriado. Os dados são transferidos do dispositivo para a nuvem com segurança pela Internet. Cada dispositivo tem um destino iSCSI que superfícies todos os volumes compartilhados nesse dispositivo. Todos os dados são criptografados antes de serem enviados para o armazenamento em nuvem. 

![Chave de criptografia de armazenamento em nuvem](./media/storsimple-security/CloudStorageEncryption.png)

Para ajudar a garantir a segurança e a integridade dos dados movidos para a nuvem, o StorSimple permite que você defina as chaves de criptografia de armazenamento em nuvem da seguinte maneira:

* Você especifica a chave de criptografia de armazenamento em nuvem ao criar um contêiner de volume. A chave não pode ser modificada ou adicionada posteriormente.
* Todos os volumes em um contêiner de volume compartilham a mesma chave de criptografia. Se você quiser uma forma diferente de criptografia para um volume específico, recomendamos que crie um novo contêiner de volume para hospedar esse volume.
* Quando você insere a chave de criptografia de armazenamento em nuvem no serviço StorSimple Device Manager, a chave é criptografada usando a parte pública da chave de criptografia de dados de serviço e, em seguida, enviada para o dispositivo.
* A chave de criptografia de armazenamento em nuvem não é armazenada em nenhum lugar no serviço e é conhecida apenas pelo dispositivo.
* A especificação de uma chave de criptografia de armazenamento em nuvem é opcional. Você pode enviar dados que foram criptografados no host para o dispositivo.

### <a name="additional-security-best-practices"></a>Práticas recomendadas de segurança adicionais

* Dividir o tráfego: Isole a SAN iSCSI do tráfego do usuário em uma LAN corporativa implantando uma rede totalmente separada e usando VLANs em que o isolamento físico não é uma opção. Uma rede dedicada para o armazenamento iSCSI garantirá a segurança e o desempenho de seus dados críticos para os negócios. Misturar armazenamento e tráfego de usuário em uma LAN corporativa não é recomendado e pode aumentar a latência e causar falhas de rede.
* Para segurança de rede no lado do host, use adaptadores de rede que dão suporte a TOE (TCP/IP Offload Engine). O TOE reduz a carga de CPU processando TCP no adaptador de rede.

## <a name="protect-data-via-storage-accounts"></a>Proteger dados por meio de contas de armazenamento

Cada assinatura Microsoft Azure pode criar uma ou mais contas de armazenamento. (Uma conta de armazenamento fornece um namespace exclusivo para trabalhar com dados armazenados na nuvem do Azure.) O acesso a uma conta de armazenamento é controlado pela assinatura e pelas chaves de acesso associadas a essa conta de armazenamento.

Quando você cria uma conta de armazenamento, Microsoft Azure gera chaves de acesso de armazenamento de 2 512 bits, uma das quais é usada para autenticação quando o dispositivo StorSimple acessa a conta de armazenamento. Observe que apenas uma dessas chaves está em uso. A outra chave é mantida em reserva, permitindo que você gire as chaves periodicamente. Para girar as chaves, torne a chave secundária ativa e, em seguida, exclua a chave primária. Em seguida, você pode criar uma nova chave para uso durante a próxima rotação. (Por motivos de segurança, muitos data centers exigem a rotação de chaves.)

Recomendamos que você siga estas práticas recomendadas para a rotação de chaves:

* Você deve girar as chaves da conta de armazenamento regularmente para ajudar a garantir que sua conta de armazenamento não seja acessada por usuários não autorizados.
* Periodicamente, o administrador do Azure deve alterar ou regenerar a chave primária ou secundária usando a seção de armazenamento da portal do Azure para acessar diretamente a conta de armazenamento.

## <a name="protect-data-via-encryption"></a>Proteger dados via criptografia

O StorSimple usa os seguintes algoritmos de criptografia para proteger os dados armazenados ou viajando entre os componentes da solução StorSimple.

| Algoritmo | Comprimento da chave | Protocolos/aplicativos/comentários |
| --- | --- | --- |
| RSA |2048 |O RSA PKCS 1 v 1.5 é usado pelo portal do Azure para criptografar dados de configuração que são enviados para o dispositivo: por exemplo, credenciais de conta de armazenamento, configuração de dispositivo StorSimple e chaves de criptografia de armazenamento em nuvem. |
| AES |256 |O AES com CBC é usado para criptografar a parte pública da chave de criptografia de dados de serviço antes de ser enviada para o portal do Azure do dispositivo StorSimple. Ele também é usado pelo dispositivo StorSimple para criptografar dados antes que os dados sejam enviados para a conta de armazenamento em nuvem. |

## <a name="storsimple-cloud-appliance-security"></a>Segurança do dispositivo de nuvem StorSimple

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="managing-personal-information"></a>Gerenciando informações pessoais

O StorSimple Device Manager para séries físicas e virtuais coleta informações pessoais nas seguintes instâncias de chave:

- Alerta as configurações de usuário em que o endereço de email dos usuários está configurado. Essas informações podem ser exibidas e limpas pelo administrador. Isso se aplica aos dispositivos StorSimple 8000 Series e às matrizes virtuais StorSimple.
  * Para exibir e limpar as configurações da série StorSimple 8000, siga as etapas em [Exibir e gerenciar alertas do StorSimple](storsimple-8000-manage-alerts.md#configure-alert-settings)
  * Para exibir e limpar as configurações do StorSimple virtual array, siga as etapas em [Exibir e gerenciar alertas do storsimple](storsimple-virtual-array-manage-alerts.md#configure-alert-settings)
- Usuários que podem acessar os dados que residem nos compartilhamentos. Uma lista de usuários que podem acessar os dados de compartilhamento é exibida e pode ser exibida. Essa lista também é excluída quando os compartilhamentos são excluídos. Isso se aplica somente a matrizes virtuais StorSimple.
  * Para exibir a lista de usuários que podem acessar ou excluir um compartilhamento, siga as etapas em [gerenciar compartilhamentos na matriz virtual StorSimple](storsimple-virtual-array-manage-shares.md)

Para obter mais informações, reveja a política de privacidade da Microsoft no [Centro de Fidedignidade](https://www.microsoft.com/trustcenter).

## <a name="frequently-asked-questions-faq"></a>Perguntas Mais Frequentes (FAQ)

A seguir estão algumas perguntas e respostas sobre segurança e Microsoft Azure StorSimple.

**P:** Meu serviço está comprometido. Quais devem ser minhas próximas etapas?

**R:** Você deve alterar imediatamente a chave de criptografia de dados de serviço e as chaves de conta de armazenamento para a conta de armazenamento que está sendo usada para hierarquizar dados. Para obter instruções, acesse:

* [Alterar a chave de criptografia de dados de serviço](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Rotação de chaves de contas de armazenamento](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**P:** Tenho um novo dispositivo StorSimple que está solicitando a chave de registro do serviço. Como fazer recuperá-lo?

**R:** Essa chave foi criada quando você criou o serviço StorSimple Device Manager pela primeira vez. Ao usar o serviço de Device Manager do StorSimple para se conectar ao dispositivo, você pode usar a página de início rápido do serviço para exibir ou regenerar a chave de registro do serviço. A geração de uma nova chave de registro de serviço não afetará os dispositivos registrados existentes. Para obter instruções, acesse:

* [Exibir ou regenerar a chave de registro do serviço](storsimple-8000-manage-service.md##regenerate-the-service-registration-key)

**P:** Perdi minha chave de criptografia de dados de serviço. O que posso fazer?

**R:** Contacte o Suporte da Microsoft. Eles podem fazer logon em uma sessão de suporte em seu dispositivo e ajudá-lo a recuperar a chave (desde que pelo menos um dispositivo esteja online). Imediatamente depois de obter a chave de criptografia de dados de serviço, você deve alterá-la para garantir que a nova chave seja conhecida apenas por você. Para obter instruções, acesse:

* [Alterar a chave de criptografia de dados de serviço](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)

**P:**  Eu autorizei um dispositivo para uma alteração de chave de criptografia de dados de serviço, mas não iniciou o processo de alteração de chave. O que devo fazer?

**R:** Se o período de tempo limite tiver expirado, será necessário reautorizar o dispositivo para a alteração da chave de criptografia de dados de serviço e iniciar o processo novamente.

**P:**  Alterei a chave de criptografia de dados de serviço, mas não pude atualizar os outros dispositivos dentro de 4 horas. É necessário iniciar novamente?

**R:** O período de tempo de 4 horas é apenas para iniciar a alteração. Depois de iniciar o processo de atualização no dispositivo StorSimple autorizado, a autorização é válida até que todos os dispositivos sejam atualizados.

**P:** Nosso administrador do StorSimple saiu da empresa. O que devo fazer?

**R:** Altere e redefina as senhas que permitem acesso ao dispositivo StorSimple e altere a chave de criptografia de dados de serviço para garantir que as novas informações não sejam conhecidas por pessoas não autorizadas. Para obter instruções, acesse:

* [Usar o serviço de Device Manager do StorSimple para alterar suas senhas do storsimple](storsimple-8000-change-passwords.md)
* [Alterar a chave de criptografia de dados de serviço](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Configurar o CHAP para seu dispositivo StorSimple](storsimple-8000-configure-chap.md)

**P:** Quero fornecer a senha de Snapshot Manager do StorSimple para um host que está se conectando ao dispositivo StorSimple, mas a senha não está disponível. O que posso fazer?

**R:** Se você esqueceu a senha, deve criar uma nova. Em seguida, lembre-se de informar todos os usuários existentes que a senha foi alterada e que eles devem atualizar seus clientes para usar a nova senha. Para obter instruções, acesse:

* [Alterar a senha de Snapshot Manager do StorSimple](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [Autenticar um dispositivo](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**P:** O certificado para acesso remoto ao Windows PowerShell para StorSimple foi alterado no dispositivo. Como fazer atualizar meus clientes de acesso remoto?

**R:** Você pode baixar o novo certificado do serviço de Device Manager do StorSimple e, em seguida, fornecê-lo para ser instalado no repositório de certificados de seus clientes de acesso remoto. Para obter instruções, acesse:

* [Cmdlet Import-Certificate](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate)

**P:** Meus dados serão protegidos se o serviço StorSimple Device Manager for comprometido?

**R:** Os dados de configuração do serviço são sempre criptografados com sua chave pública quando você o exibe em um navegador da Web. Como o serviço não tem acesso à chave privada, o serviço não poderá ver nenhum dado. Se o serviço StorSimple Device Manager for comprometido, não haverá impacto, pois não há chaves armazenadas no serviço StorSimple Device Manager.

**P:** Se alguém obtiver acesso ao certificado de criptografia de dados, meus dados serão comprometidos?

**R:** Microsoft Azure armazena a chave de criptografia de dados do cliente (arquivo. pfx) em um formato criptografado. Como o arquivo. pfx é criptografado e o serviço StorSimple não tem a chave de criptografia de dados de serviço para descriptografar o arquivo. pfx, simplesmente obter acesso ao arquivo. pfx não vai expor nenhum segredo.

**P:** O que acontece se uma entidade governamental perguntar aos meus dados da Microsoft?

**R:** Como todos os dados são criptografados no serviço e a chave privada é mantida com o dispositivo, a entidade governamental deve solicitar os dados ao cliente.



## <a name="next-steps"></a>Passos seguintes

[Implante seu dispositivo StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

