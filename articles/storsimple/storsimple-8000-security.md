---
title: Segurança de série 8000 do StorSimple | Documentos da Microsoft
description: Descreve as funcionalidades de segurança e privacidade que protegem o seu serviço, dispositivos e dados no local e na cloud do StorSimple.
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
ms.openlocfilehash: 734b0cf9373ea98ab33c06b45ad53b46a3355dd6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62117030"
---
# <a name="storsimple-security-and-data-protection"></a>Proteção de dados e segurança do StorSimple

## <a name="overview"></a>Descrição geral

A segurança é uma preocupação importante para qualquer pessoa que está a adotar uma nova tecnologia, especialmente quando a tecnologia é utilizada com dados confidenciais ou proprietários. Durante a avaliação tecnologias diferentes, tem de considerar o aumento dos riscos e custos para proteção de dados. O Microsoft Azure StorSimple fornece tanto uma solução de segurança e privacidade para a proteção de dados, ajudando a garantir:

* **Confidencialidade** – apenas as entidades autorizadas podem ver os seus dados.
* **Integridade** – apenas as entidades autorizadas podem modificar ou eliminar os seus dados.

A solução Microsoft Azure StorSimple é composta por quatro componentes principais que interagem entre si:

* **Serviço StorSimple Device Manager alojado no Microsoft Azure** – o serviço de gestão que utiliza para configurar e aprovisionar o dispositivo StorSimple.
* **Dispositivo StorSimple** – um dispositivo físico instalado no seu datacenter. Todos os anfitriões e clientes que geram dados ligar ao dispositivo StorSimple e o dispositivo gere os dados e move-o para a cloud do Azure conforme adequado.
* **Os clientes/anfitriões ligados ao dispositivo** – os clientes na sua infraestrutura de que se ligam ao dispositivo StorSimple e geram dados de que precisam ser protegido.
* **Armazenamento na cloud** – A localização na cloud do Azure onde os dados são armazenados.

As secções seguintes descrevem os recursos de segurança do StorSimple que ajudam a proteger a cada um desses componentes e os dados armazenados nos mesmos. Ele também inclui uma lista de perguntas que possa ter sobre segurança do Microsoft Azure StorSimple e as respostas correspondentes.

## <a name="storsimple-device-manager-service-protection"></a>Proteção de serviço do StorSimple Device Manager

O serviço StorSimple Device Manager é um serviço de gestão alojado no Microsoft Azure e utilizado para gerir todos os dispositivos do StorSimple que sua organização tiver obtido. Pode aceder ao serviço de Gestor de dispositivos do StorSimple, utilizando as credenciais organizacionais para iniciar sessão no portal do Azure através de um navegador da web.

Acesso ao serviço StorSimple Device Manager requer que sua organização tenha uma subscrição do Azure, que inclui o StorSimple. A subscrição controla as funcionalidades a que pode aceder no portal do Azure. Se sua organização não tiver uma subscrição do Azure e pretender saber mais sobre as mesmas, veja [Inscreva-se para o Azure como uma organização](../active-directory/fundamentals/sign-up-organization.md).

Uma vez que o serviço StorSimple Device Manager está alojado no Azure, é protegida pelos recursos de segurança do Azure. Para obter mais informações sobre as funcionalidades de segurança fornecidas pelo Microsoft Azure, aceda ao [Centro de Fidedignidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

## <a name="storsimple-device-protection"></a>Proteção de dispositivos do StorSimple

O dispositivo StorSimple é um dispositivo de armazenamento híbrida no local que contenha unidades de estado sólido (SSDs) e unidades de disco rígido (HDDs), juntamente com os controladores de redundantes e capacidades de ativação pós-falha automática. Os controladores de gestão do armazenamento em camadas, colocar atualmente utilizados (ou frequente) dados no armazenamento local (no StorSimple no local ou de dispositivo servidores), ao mover menos dados utilizados com frequência para a cloud.

Apenas autorizados StorSimple, os dispositivos têm permissão para associar o serviço StorSimple Device Manager que criou na sua subscrição do Azure. Para autorizar um dispositivo, tem de registá-lo com o serviço StorSimple Device Manager, fornecendo a chave de registo do serviço. A chave de registo do serviço é uma chave aleatória de 128 bits gerada no portal do Azure.

![chave de registo do serviço](./media/storsimple-security/ServiceRegistrationKey.png)

Para saber como obter uma chave de registo do serviço, aceda a [passo 2: Obter a chave de registo do serviço](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key).

A chave de registo do serviço é uma chave de tempo que contenha mais de 100 carateres. Pode copiar a chave e guarde-o num ficheiro de texto numa localização segura para que pode usá-lo para autorizar dispositivos adicionais conforme necessário. Se a chave de registo do serviço for perdida, depois de registar o dispositivo primeiro, pode gerar uma nova chave do serviço StorSimple Device Manager. Isto não irá afetar o funcionamento dos dispositivos existentes.

Depois de um dispositivo é registado, ele usa tokens para comunicar com o Microsoft Azure. A chave de registo do serviço não é utilizada após o registo do dispositivo.

> [!NOTE]
> Recomendamos que regenere a chave de registo do serviço após cada utilização.


## <a name="protect-your-storsimple-solution-via-passwords"></a>Proteger a sua solução StorSimple através de palavras-passe

As palavras-passe são um aspeto importante da segurança do computador e são amplamente usadas na solução StorSimple para ajudar a garantir que os dados estão acessíveis aos utilizadores autorizados apenas. O StorSimple permite-lhe configurar as seguintes palavras-passe:

* Palavra-passe do administrador de dispositivo do StorSimple
* Desafie palavras-passe iniciador e destino de protocolo de autenticação de Handshake (CHAP)
* Palavra-passe do Snapshot Manager StorSimple

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Windows PowerShell para StorSimple e a senha de administrador de dispositivo do StorSimple

Windows PowerShell para StorSimple é uma interface de linha de comandos que pode utilizar para gerir o dispositivo StorSimple. Windows PowerShell para StorSimple tem recursos que permitem-lhe registar o seu dispositivo, configure a interface de rede no seu dispositivo, instalar determinados tipos de atualizações, resolver problemas relacionados com o seu dispositivo ao aceder a sessão de suporte e alteram o estado do dispositivo. Pode acessar a Windows PowerShell para StorSimple, conectando-se à consola de série do dispositivo ou ao utilizar a comunicação remota do Windows PowerShell.

Comunicação remota do PowerShell pode ser feita através de HTTPS ou HTTP. Se estiver ativada a gestão remota através de HTTPS, terá de transferir o certificado de gestão remota do dispositivo e instalá-lo no cliente remoto. Para obter mais informações sobre a comunicação remota do PowerShell, aceda a [ligar remotamente ao dispositivo StorSimple](storsimple-8000-remote-connect.md).

Depois de utilizar o Windows PowerShell para StorSimple para ligar ao dispositivo, terá de fornecer a palavra-passe de administrador do dispositivo para iniciar sessão no dispositivo.

![Palavra-passe do administrador do dispositivo](./media/storsimple-security/DeviceAdminPW.png)

Mantenha as seguintes práticas recomendadas em mente:

* Gestão remota está desativada por predefinição. Pode utilizar o serviço StorSimple Device Manager para ativá-la. Como prática recomendada de segurança, acesso remoto deve ser ativado apenas durante o período de tempo que é realmente necessária.
* Se alterar a palavra-passe, certifique-se de que todos os utilizadores de acesso remoto de notificar para que eles não se deparam com uma perda de conectividade inesperados.
* O serviço StorSimple Device Manager não é possível obter palavras-passe existentes: só pode repô-los. Recomendamos que armazene todas as senhas num local seguro para que não é necessário repor uma palavra-passe, se ele é esquecido. Se precisar de repor uma palavra-passe, certifique-se de que notificar todos os utilizadores antes de repor-lo.

Pode acessar a interface do Windows PowerShell usando uma conexão serial no dispositivo. Também pode acessá-lo remotamente utilizando HTTP ou HTTPS que fornecer segurança adicional. HTTPS fornece um nível mais elevado de segurança do que um número de série ou de uma ligação HTTP. No entanto, para utilizar HTTPS, tem primeiro de instalar um certificado no computador cliente que irá aceder ao dispositivo. Pode transferir o certificado de acesso remoto a partir da página de configuração do dispositivo no serviço StorSimple Device Manager. Se o certificado para acesso remoto for perdido, tem de transferir um novo certificado e propagá-lo a todos os clientes que estão autorizados a utilizar a gestão remota.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Desafie palavras-passe iniciador e destino de protocolo de autenticação de Handshake (CHAP)

CHAP é um esquema de autenticação utilizado pelo dispositivo StorSimple para validar a identidade de clientes remotos. A verificação baseia-se uma palavra-passe partilhada. CHAP pode ser unidirecional (unidirecionais) ou mútua (bidirecional). Com CHAP unidirecional, o destino (o dispositivo StorSimple) autentica um iniciador (anfitrião). CHAP inverso ou mútua requer que o destino autenticar o iniciador e, em seguida, o iniciador autenticar o destino. StorSimple pode ser configurado para utilizar qualquer um dos métodos.

Tenha em atenção o seguinte quando configurar o CHAP:

* O nome de utilizador CHAP tem de conter menos de 233 carateres.
* A palavra-passe CHAP tem de ter entre 12 a 16 carateres. A tentar utilizar um nome de utilizador mais tempo ou palavra-passe irá resultar numa falha de autenticação do anfitrião do Windows.
* Não é possível utilizar a mesma palavra-passe para o iniciador CHAP e o destino do CHAP.
* Depois de definir a palavra-passe, ele pode ser alterado, mas não é possível obtê-la. Se a palavra-passe é alterada, certifique-se de que todos os utilizadores de acesso remoto de notificar para que eles consegue ligar com êxito para o dispositivo StorSimple.

Para obter mais informações sobre o CHAP e como configurá-lo para a sua solução StorSimple, aceda a [configurar o CHAP para o dispositivo StorSimple](storsimple-8000-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>Palavra-passe do Snapshot Manager StorSimple

Snapshot Manager do StorSimple é um snap-in da consola de gestão da Microsoft (MMC), que utiliza grupos de volumes e o serviço de cópia de sombra de Volume do Windows para gerar as cópias de segurança consistentes com aplicações. Além disso, pode utilizar o Snapshot Manager do StorSimple para criar agendas de cópia de segurança e clonar ou restaurar volumes.

Quando configurar um dispositivo para utilizar o Snapshot Manager do StorSimple, será solicitado para fornecer a palavra-passe do Snapshot Manager do StorSimple. Esta palavra-passe em primeiro lugar é definido no Windows PowerShell para StorSimple durante o registo. A palavra-passe também pode ser definida e em vez do serviço StorSimple Device Manager. Esta palavra-passe autentica o dispositivo StorSimple Snapshot Manager.

![Palavra-passe do Snapshot Manager StorSimple](./media/storsimple-security/SnapshotMgrPassword.png)

A palavra-passe do Snapshot Manager do StorSimple tem de ter 14 a 15 carateres e tem de conter 3 ou mais de uma combinação de carateres em maiúsculas, minúsculas, numéricos e especiais. Depois de definir a palavra-passe do Snapshot Manager do StorSimple, pode ser alterado, mas não é possível obtê-la. Se alterar a palavra-passe, certifique-se de que notificar todos os utilizadores.

Para obter mais informações sobre o StorSimple Snapshot Manager, aceda a [o que é o Snapshot Manager do StorSimple?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Melhores práticas de palavra-passe

Recomendamos que utilize as seguintes diretrizes para ajudar a garantir que as palavras-passe do StorSimple são fortes e bem protegida:

* Altere as palavras-passe a cada três meses. Alterar as palavras-passe é imposta anualmente.
* Utilize palavras-passe seguras. Para obter mais informações, aceda a [criar palavras-passe mais fortes e protegê-los](https://cloudblogs.microsoft.com/microsoftsecure/2014/08/25/create-stronger-passwords-and-protect-them/).
* Sempre utilizem senhas diferentes para mecanismos de acesso diferentes; cada uma das palavras-passe que especificar deve ser exclusiva.
* Não partilhe as palavras-passe com qualquer pessoa que não está autorizado a aceder ao dispositivo StorSimple.
* Não falar sobre uma palavra-passe à frente de outras pessoas ou sugestão em conta o formato de uma palavra-passe.
* Se suspeitar de que uma conta ou palavra-passe foi comprometida, relatar o incidente para o seu departamento de segurança de informações.
* Trate todas as senhas como informações confidenciais, confidenciais. 

## <a name="storsimple-data-protection"></a>Proteção de dados do StorSimple

Esta secção descreve as funcionalidades de segurança do StorSimple que protegem os dados em trânsito e dados armazenados.

Conforme descrito noutras secções, palavras-passe são utilizadas para autorize e autentique os utilizadores antes de obterem acesso à sua solução StorSimple. Outra consideração de segurança está a proteger dados contra utilizadores não autorizados enquanto estiverem a ser transferido entre sistemas de armazenamento e enquanto a ser armazenado. As secções seguintes descrevem as funcionalidades de proteção de dados fornecidas com o StorSimple.

> [!NOTE]
> A eliminação de duplicados proporciona proteção adicional para dados armazenados no dispositivo StorSimple e do armazenamento do Microsoft Azure. Quando tem eliminação de dados duplicados, os objetos de dados são armazenados em separado, de metadados utilizados para mapear e acessá-los do: não existe nenhum contexto de nível de armazenamento disponível para reconstruir os dados com base na estrutura de volume, o sistema de ficheiros ou o nome de ficheiro.


## <a name="protect-data-flowing-through-the-service"></a>Proteger os dados que flui através do serviço

É o objetivo principal do serviço StorSimple Device Manager gerir e configurar o dispositivo StorSimple. O serviço StorSimple Device Manager é executado no Microsoft Azure. Utilize o portal do Azure para introduzir dados de configuração do dispositivo e, em seguida, o Microsoft Azure utiliza o serviço StorSimple Device Manager para enviar os dados para o dispositivo. O StorSimple utiliza um sistema de pares de chaves assimétricas para ajudar a garantir que um comprometimento do serviço do Azure não resultará num comprometimento de informações armazenadas.

![Encriptação de dados em trânsito](./media/storsimple-security/DataEncryption.png)

O sistema de chave assimétrico ajuda a proteger os dados que fluem através do serviço da seguinte forma:

1. Um certificado de encriptação de dados que utiliza uma chave pública e privada assimétrica par é gerado no dispositivo e é utilizado para proteger os dados. As chaves são geradas quando o primeiro dispositivo está registado.
2. As chaves de certificado de encriptação de dados são exportadas para um ficheiro Personal Information Exchange (. pfx) que está protegido pela chave de encriptação de dados do serviço, que é uma chave de 128 bits forte, que é gerada aleatoriamente, o primeiro dispositivo durante o registo.
3. A chave pública do certificado de forma segura esteja disponível para o serviço StorSimple Device Manager e a chave privada permanece com o dispositivo.
4. Introduzir o serviço de dados são encriptados com o público chave e desencriptado utilizando a chave privada armazenada no dispositivo, garantindo que o serviço do Azure não é possível desencriptar os dados que fluem para o dispositivo.

A chave de encriptação de dados do serviço é gerada no apenas o primeiro dispositivo registado no serviço. Todos os dispositivos subsequentes que estão registados com o serviço tem de utilizar a mesma chave de encriptação de dados do serviço.

> [!IMPORTANT]
> É muito importante fazer uma cópia da chave de encriptação de dados de serviço e guardá-lo numa localização segura. Uma cópia da chave de encriptação de dados de serviço deve ser armazenada de forma que ele possa ser acessado por uma pessoa autorizada e pode ser facilmente comunicado para o administrador do dispositivo.
> 
> Se perder a chave de encriptação de dados do serviço, uma pessoa de suporte da Microsoft pode ajudar a recuperá-la, desde que tenha, pelo menos, um dispositivo num estado online. Recomendamos que altere a chave de encriptação de dados do serviço após obtê-la.

Para alterar a chave de encriptação de dados do serviço e o certificado de encriptação de dados correspondente, siga os passos em [alterar a chave de encriptação de dados do serviço para o seu serviço StorSimple Device Manager](storsimple-8000-manage-service.md#change-the-service-data-encryption-key). Alterar as chaves de encriptação requer que todos os dispositivos sejam atualizadas com a nova chave. Por conseguinte, recomendamos que altere a chave de quando todos os dispositivos estão online. Se os dispositivos estiverem offline, as chaves podem ser alteradas numa hora diferente. Os dispositivos com chaves desatualizados ainda será capazes de executar cópias de segurança, mas eles não será possível restaurar os dados até que a chave é atualizada.

A chave de encriptação de dados do serviço e o certificado de encriptação de dados não expiram. No entanto, recomendamos que altere a chave de encriptação de dados do serviço anualmente para ajudar a evitar o compromisso de chave.

## <a name="protect-data-at-rest"></a>Proteger dados em repouso

O dispositivo StorSimple gere dados armazenando-os em escalões localmente e na cloud, dependendo da frequência de utilização. Todas as máquinas de host que estão ligadas ao dispositivo enviam dados para o dispositivo, o que, em seguida, move os dados para a cloud, conforme apropriado. Dados são transferidos do dispositivo para a cloud em segurança através da Internet. Cada dispositivo tem um destino de iSCSI que analisa todos os volumes partilhados nesse dispositivo. Todos os dados são encriptados antes de ser enviada para o armazenamento na nuvem. 

![chave de encriptação de armazenamento na nuvem](./media/storsimple-security/CloudStorageEncryption.png)

Para ajudar a garantir a segurança e integridade dos dados movidos para a cloud, o StorSimple permite-lhe definir as chaves de encriptação de armazenamento na cloud da seguinte forma:

* Especifique a chave de encriptação de armazenamento na cloud ao criar um contentor de volume. A chave não pode ser modificada ou adicionada mais tarde.
* Todos os volumes num contentor de volume partilham a mesma chave de encriptação. Se pretender que um formato diferente de encriptação para um volume específico, recomendamos que crie um novo contentor de volume para hospedar esse volume.
* Ao introduzir a chave de encriptação de armazenamento na cloud no serviço StorSimple Device Manager, a chave for encriptada com a parte pública da chave de encriptação de dados de serviço e, em seguida, enviado para o dispositivo.
* A chave de encriptação de armazenamento na cloud não é armazenada em qualquer lugar no serviço e só conhece o dispositivo.
* Especificar uma chave de encriptação de armazenamento na nuvem é opcional. Pode enviar dados que foi encriptados em anfitrião para o dispositivo.

### <a name="additional-security-best-practices"></a>Melhores práticas de segurança adicionais

* Dividir o tráfego: isolar sua SAN de iSCSI do tráfego do utilizador numa LAN corporativa ao implementar uma rede totalmente separada e a utilizar VLANs onde isolamento físico não é uma opção. Uma rede dedicada para o armazenamento iSCSI garantirá a segurança e o desempenho dos seus dados críticos. Misturar o tráfego de armazenamento e o utilizador ao longo de uma LAN corporativa não é recomendada e pode aumentar a latência e causar falhas de rede.
* Para segurança de rede do lado do host, use as interfaces de rede que suportam o motor de descarga de TCP/IP (TOE). TOE reduz a carga da CPU ao processamento de TCP no adaptador de rede.

## <a name="protect-data-via-storage-accounts"></a>Proteger dados através de contas de armazenamento

Cada subscrição do Microsoft Azure, pode criar uma ou mais contas de armazenamento. (Uma conta de armazenamento fornece um espaço de nomes exclusivo para trabalhar com dados armazenados na cloud do Azure.) Acesso a uma conta de armazenamento é controlado pelas chaves de acesso e de subscrição associadas essa conta de armazenamento.

Quando cria uma conta de armazenamento, o Microsoft Azure gera duas chaves de acesso de armazenamento de 512 bits, um dos quais é utilizado para autenticação quando o dispositivo StorSimple acede a conta de armazenamento. Tenha em atenção que apenas uma dessas chaves está em uso. A outra chave é mantida em reserva, permitindo-lhe rodar as chaves periodicamente. Para rodar chaves, tornar a chave secundária ativa e, em seguida, elimine a chave primária. Em seguida, pode criar uma nova chave para utilização durante a próxima rotação. (Por motivos de segurança, muitos data Centers exigem rotação de chaves.)

Recomendamos que siga estas práticas recomendadas para a rotação de chaves:

* Deve rodar chaves de conta de armazenamento regularmente para ajudar a garantir que sua conta de armazenamento não é acessada por utilizadores não autorizados.
* Periodicamente, o administrador do Azure deve alterar ou voltar a gerar a chave primária ou secundária, utilizando a secção de armazenamento do portal do Azure para aceder diretamente à conta de armazenamento.

## <a name="protect-data-via-encryption"></a>Proteger dados através de encriptação

StorSimple utiliza os seguintes algoritmos de encriptação para proteger os dados armazenados no ou em trânsito entre os componentes da solução StorSimple.

| Algoritmo | Comprimento da chave | Protocolos/aplicações/comentários |
| --- | --- | --- |
| RSA |2048 |RSA PKCS 1 v1.5 é utilizado pelo portal do Azure para encriptar os dados de configuração que são enviados para o dispositivo: por exemplo, o armazenamento de credenciais, configuração do dispositivo StorSimple, de contas e chaves de encriptação de armazenamento da cloud. |
| AES |256 |AES com CBC é utilizado para encriptar a parte pública da chave de encriptação de dados de serviço antes de ser enviada para o portal do Azure do dispositivo StorSimple. Ele também é usado pelo dispositivo StorSimple para encriptar dados antes dos dados são enviados para a conta de armazenamento na cloud. |

## <a name="storsimple-cloud-appliance-security"></a>Segurança do StorSimple Cloud Appliance

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="managing-personal-information"></a>Gerenciamento de informações pessoais

O Gestor de dispositivos StorSimple físicos e virtuais de série recolhe as informações pessoais nas seguintes instâncias principais:

- Definições de utilizador em que o endereço de correio eletrónico de utilizadores estão configurados de alerta. Estas informações podem ser visualizadas e limpos pelo administrador. Isto aplica-se aos dispositivos da série StorSimple 8000 e as matrizes virtuais do StorSimple.
  * Para visualizar e limpar as definições para a série StorSimple 8000, siga os passos em [ver e gerir alertas do StorSimple](storsimple-8000-manage-alerts.md#configure-alert-settings)
  * Para visualizar e limpar as definições do StorSimple Virtual Array, siga os passos em [ver e gerir alertas do StorSimple](storsimple-virtual-array-manage-alerts.md#configure-alert-settings)
- Utilizadores que podem aceder os dados que residem nas partilhas de. Uma lista de utilizadores que podem aceder os dados de partilha é apresentada e pode ser visualizada. Esta lista também é eliminada quando as partilhas é eliminado. Isto aplica-se apenas para as matrizes virtuais do StorSimple.
  * Para ver a lista de utilizador que pode aceder ou eliminar uma partilha, siga os passos em [gerir partilhas na matriz Virtual StorSimple](storsimple-virtual-array-manage-shares.md)

Para obter mais informações, reveja a política de privacidade da Microsoft no [Centro de Fidedignidade](https://www.microsoft.com/trustcenter).

## <a name="frequently-asked-questions-faq"></a>Perguntas Mais Frequentes (FAQ)

Seguem-se algumas perguntas e respostas sobre a segurança e o Microsoft Azure StorSimple.

**P:** Meu serviço for comprometido. O que deve ser meus passos seguintes?

**R:** Imediatamente deve alterar a chave de encriptação de dados do serviço e as chaves de conta de armazenamento para a conta de armazenamento que está a ser utilizado para camadas de dados. Para obter instruções, aceda a:

* [Alterar a chave de encriptação de dados do serviço](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Rotação de chaves de contas de armazenamento](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**P:** Tenho um novo dispositivo do StorSimple que pede para a chave de registo do serviço. Como recuperá-la?

**R:** Esta chave foi criada quando criou o serviço StorSimple Device Manager pela primeira vez. Quando utiliza o serviço StorSimple Device Manager para ligar ao dispositivo, pode utilizar a página de início rápido do serviço para ver ou regenerar a chave de registo do serviço. Gerar uma nova chave de registo do serviço não irá afetar os dispositivos registados existentes. Para obter instruções, aceda a:

* [Ver ou voltar a gerar a chave de registo do serviço](storsimple-8000-manage-service.md##regenerate-the-service-registration-key)

**P:** Eu perdi minha chave de encriptação de dados do serviço. O que posso fazer?

**R:** Contacte o Suporte da Microsoft. Pode iniciar sessão a uma sessão de suporte no seu dispositivo e o ajuda a obter a chave (desde que, pelo menos, um dispositivo está online). Imediatamente depois de obter a chave de encriptação de dados do serviço, deve alterar de modo a garantir que a nova chave só conhece. Para obter instruções, aceda a:

* [Alterar a chave de encriptação de dados do serviço](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)

**P:**  Autorizado de um dispositivo para uma alteração de chaves de encriptação do serviço dados, mas não iniciou o processo de alteração de chaves. O que devo fazer?

**R:** Se o período de tempo limite expirou, terá de autorizar o dispositivo para que a alteração de chaves de encriptação do serviço dados e iniciar o processo novamente.

**P:**  Alterei a chave de encriptação de dados do serviço, mas não foi possível atualizar os outros dispositivos dentro de 4 horas. É necessário que começar de novo?

**R:** O período de tempo de 4 horas é apenas para iniciar a alteração. Depois de iniciar o processo de atualização no dispositivo StorSimple autorizado, a autorização é válida até que todos os dispositivos sejam atualizados.

**P:** O administrador do StorSimple tiver saído da empresa. O que devo fazer?

**R:** Alterar e repor as palavras-passe que permitem o acesso ao dispositivo StorSimple e alterar a chave de encriptação de dados do serviço para se certificar de que as novas informações não são conhecidas por pessoa não-autorizada. Para obter instruções, aceda a:

* [Utilize o serviço StorSimple Device Manager para alterar as palavras-passe do storsimple](storsimple-8000-change-passwords.md)
* [Alterar a chave de encriptação de dados do serviço](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Configurar o CHAP para o dispositivo StorSimple](storsimple-8000-configure-chap.md)

**P:** Quero fornecer a palavra-passe do Snapshot Manager do StorSimple para um anfitrião que está a ligar ao dispositivo StorSimple, mas a palavra-passe não está disponível. O que posso fazer?

**R:** Se se tiver esquecido da palavra-passe, deve criar um novo. Em seguida, certifique-se de que todos os utilizadores existentes de informar que a palavra-passe foi alterada e que eles devem atualizar seus clientes para utilizar a nova palavra-passe. Para obter instruções, aceda a:

* [Alterar a palavra-passe do Snapshot Manager do StorSimple](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [Autenticar um dispositivo](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**P:** O certificado para acesso remoto para o Windows PowerShell para StorSimple foi alterado no dispositivo. Como posso atualizar meus clientes de acesso remoto?

**R:** Pode transferir o novo certificado do serviço StorSimple Device Manager e, em seguida, forneça-o para ser instalado no arquivo de certificados, os clientes de acesso remoto. Para obter instruções, aceda a:

* [Cmdlet de importar certificado](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate)

**P:** É meu if protegido de dados o StorSimple Device Manager serviço for comprometido?

**R:** Dados de configuração de serviço são sempre encriptados com a chave pública quando vê-la num navegador da web. Uma vez que o serviço não tem acesso à chave privada, o serviço não será capaz de ver todos os dados. Se o serviço StorSimple Device Manager for comprometido, não há nenhum impacto, pois não há nenhum chaves armazenadas no serviço StorSimple Device Manager.

**P:** Se alguém obtiver acesso ao certificado de encriptação de dados, serão meus dados comprometidos?

**R:** Microsoft Azure armazena a chave de encriptação de dados do cliente (ficheiro. pfx) num formato encriptado. Uma vez que o ficheiro. pfx é encriptado e o serviço de StorSimple não tem a chave de encriptação de dados do serviço para desencriptar o ficheiro. pfx, simplesmente obter acesso ao ficheiro. pfx não irá expor quaisquer segredos.

**P:** O que acontece se uma entidade governamental pedirem à Microsoft os meus dados?

**R:** Uma vez que todos os dados são encriptados no serviço e a chave privada é mantida com o dispositivo, a entidade governamental tem de pedir o cliente para os dados.



## <a name="next-steps"></a>Passos Seguintes

[Implementar o dispositivo StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

