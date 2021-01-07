---
title: Gerir sensores individuais
description: Saiba como gerir sensores individuais, incluindo gerir ficheiros de ativação, realizar backups e atualizar um sensor autónomo.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/22/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: daaca1d7c6cf43f69241e6a23f8bdfaf4015ba23
ms.sourcegitcommit: 8f0803d3336d8c47654e119f1edd747180fe67aa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2021
ms.locfileid: "97976950"
---
# <a name="manage-individual-sensors"></a>Gerir sensores individuais

Este artigo descreve como gerir sensores individuais. As tarefas incluem gerir ficheiros de ativação, realizar backups e atualizar um sensor autónomo.

Também é possível realizar determinadas tarefas de gestão de sensores a partir da consola de gestão de bordo, onde vários sensores podem ser geridos simultaneamente.

Utilize o portal Azure para sensores de bordo e registo.

## <a name="manage-sensor-activation-files"></a>Gerir ficheiros de ativação de sensores

O seu sensor estava a bordo com o Azure Defender para IoT a partir do portal Azure. Cada sensor estava a bordo como um sensor ligado localmente ou um sensor ligado à nuvem.

Um ficheiro de ativação único é enviado para cada sensor que implementa. Para obter mais informações sobre quando e como utilizar um novo ficheiro, consulte [os novos ficheiros de ativação](#upload-new-activation-files)do Upload . Se não conseguir carregar o ficheiro, consulte o upload do [ficheiro de ativação de resolução de problemas](#troubleshoot-activation-file-upload).

### <a name="about-activation-files-for-locally-connected-sensors"></a>Sobre ficheiros de ativação para sensores conectados localmente

Os sensores ligados localmente estão associados a uma subscrição do Azure. O ficheiro de ativação dos sensores ligados localmente contém uma data de validade. Um mês antes desta data, uma mensagem de aviso aparece no topo da consola do sensor. O aviso permanece até que tenha atualizado o ficheiro de ativação.

:::image type="content" source="media/how-to-manage-individual-sensors/system-setting-screenshot.png" alt-text="A imagem das definições do sistema.":::

Pode continuar a trabalhar com o Defender para funcionalidades IoT mesmo que o ficheiro de ativação tenha expirado. 

### <a name="about-activation-files-for-cloud-connected-sensors"></a>Sobre ficheiros de ativação para sensores ligados à nuvem

Os sensores que estão ligados à nuvem estão associados ao Centro Defender para IoT. Estes sensores não são limitados por períodos de tempo para o ficheiro de ativação. O ficheiro de ativação de sensores ligados à nuvem é utilizado para garantir a ligação ao centro defender para ioT.

### <a name="upload-new-activation-files"></a>Carregar novos ficheiros de ativação

Pode ser necessário carregar um novo ficheiro de ativação para um sensor a bordo quando:

- Um ficheiro de ativação expira num sensor ligado localmente. 

- Quer trabalhar num modo de gestão de sensores diferente. 

- Pretende atribuir um novo Defender para o hub IoT a um sensor ligado à nuvem.

Para adicionar um novo ficheiro de ativação:

1. Vá à página **de Gestão de Sensores.**

2. Selecione o sensor para o qual pretende carregar um novo ficheiro de ativação.

3. Apague-o.

4. A bordo do sensor novamente a partir da página **de Embarque** no novo modo ou com um novo Hub Defender para IoT.

5. Descarregue o ficheiro de ativação a partir da página Ficheiro de **Ativação de Descarregamento.**

6. Guarde o ficheiro.

    :::image type="content" source="media/how-to-manage-individual-sensors/download-activation-file.png" alt-text="Descarregue o ficheiro de ativação do Defender para o hub IoT.":::

7. Inscreva-se no Defender para consola de sensores IoT.

8. Na consola do sensor, selecione  >  **Reativação** de Definições do Sistema .

    :::image type="content" source="media/how-to-manage-individual-sensors/reactivation.png" alt-text="Seleção de reativação no ecrã de Definições do Sistema.":::

9. Selecione **Upload** e selecione o ficheiro que guardou.

    :::image type="content" source="media/how-to-manage-individual-sensors/upload-the-file.png" alt-text="Faça o upload do ficheiro que guardou.":::

10. **Selecione Ativar**.

### <a name="troubleshoot-activation-file-upload"></a>Upload de ficheiros de ativação de resolução de problemas

Receberá uma mensagem de erro se o ficheiro de ativação não puder ser carregado. Podem ter ocorrido os seguintes acontecimentos:

- **Para sensores ligados localmente**: O ficheiro de ativação não é válido. Se o ficheiro não for válido, aceda ao portal Defender para IoT. Na página **'Gestão de Sensores',** selecione o sensor com o ficheiro inválido e descarregue um novo ficheiro de ativação.

- **Para sensores ligados à nuvem:** O sensor não pode ligar-se à internet. Verifique a configuração da rede do sensor. Se o seu sensor precisar de ser conectado através de um representante web para aceder à internet, verifique se o seu servidor proxy está configurado corretamente no ecrã de **Configuração da Rede de Sensores.** Verifique se \* .azure-devices.net:443 é permitido na firewall e/ou proxy. Se os wildcards não forem suportados ou se pretender mais controlo, o FQDN para o seu hub específico Defender para IoT deve ser aberto na sua firewall e/ou proxy. Para mais detalhes, consulte [pontos finais reference - IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints).  

- **Para sensores ligados à nuvem**: O ficheiro de ativação é válido, mas o Defender para IoT rejeitou-o. Se não conseguir resolver este problema, pode descarregar outra ativação a partir da página de Gestão de **Sensores** do portal Defender para IoT. Se isto não funcionar, contacte o Microsoft Support.

## <a name="manage-certificates"></a>Gerir certificados

Após a instalação do sensor, é gerado um certificado auto-assinado local e utilizado para aceder à aplicação web do sensor. Ao iniciar sessão no sensor pela primeira vez, os utilizadores do Administrador são solicitados a fornecer um certificado SSL/TLS.  Para obter mais informações sobre a primeira configuração, consulte [iniciar sção e ative um sensor](how-to-activate-and-set-up-your-sensor.md).

Este artigo fornece informações sobre a atualização de certificados, trabalhando com comandos CLI certificados, e certificados e parâmetros de certificado suportados.

### <a name="about-certificates"></a>Acerca de certificados

O Azure Defender for IoT utiliza certificados SSL/TLS para:

1. Satisfaça os requisitos específicos de certificado e encriptação solicitados pela sua organização, fazendo o upload do certificado assinado pela AC.

1. Permitir validação entre a consola de gestão e os sensores conectados, e entre uma consola de gestão e uma consola de gestão de Alta Disponibilidade. As validações são avaliadas com uma Lista de Revogação de Certificados e a data de validade do certificado. **Se a validação falhar, a comunicação entre a consola de gestão e o sensor é interrompida e um erro de validação é apresentado na consola. Esta opção é ativada por padrão após a instalação.**

 Regras de encaminhamento de terceiros, por exemplo, informações de alerta enviadas para sYSLOG, Splunk ou ServiceNow; ou a comunicação com o Ative Directory não é validada.

### <a name="update-certificates"></a>Certificados de atualização

Os utilizadores do Administrador de Sensores podem atualizar os certificados.

Para atualizar um certificado:  

1. Selecione **Definições do sistema**.
1. Selecione **Certificados SSL/TLS.**
1. Apague ou edite o certificado e adicione um novo.
    - Adicione um nome de certificado.
    - Faça o upload de um ficheiro CRT e do ficheiro chave e introduza uma palavra-passe.
    - Faça o upload de um ficheiro PEM, se necessário.

Para alterar a definição de validação:

1. Ativar ou desativar a validação do certificado de **ativação.**
1. Selecione **Save** (Guardar).

Se a opção estiver ativada e a validação falhar, a comunicação entre a consola de gestão e o sensor é interrompida e é apresentado um erro de validação na consola.

### <a name="certificate-support"></a>Apoio a Certificados

São suportados os seguintes certificados:

- Infraestrutura-chave privada /empresarial (PKI privado) 
- Infraestruturas de chaves públicas (PKI público) 
- Gerado localmente no aparelho (auto-assinado localmente). **Não é recomendada a utilização de certificados auto-assinados.** Esta ligação é *insegura* e deve ser utilizada apenas para ambientes de ensaio. O proprietário do certificado não pode ser validado e a segurança do seu sistema não pode ser mantida. Os certificados auto-assinados nunca devem ser utilizados para redes de produção.  

Os seguintes parâmetros são suportados. Certificado CRT

- O ficheiro de certificado primário para o seu nome de domínio
- Algoritmo de assinatura = SHA256RSA
- Algoritmo de hash de assinatura = SHA256
- Válido a partir de = Data passada válida
- Válido Para = Data futura válida
- Chave Pública = RSA 2048bits (Mínimo) ou 4096bits
- Ponto de Distribuição CRL = URL para ficheiro .crl
- Assunto CN = URL, pode ser um certificado wildcard, por exemplo, example.contoso.com ou  *.contoso.com**
- Objeto (C)ountry = definido, por exemplo, EUA
- Unidade org (OU) = definida, por exemplo, Contoso Labs
- Assunto (O)rganização = definido, por exemplo, Contoso Inc.

Ficheiro chave

- O ficheiro chave gerado quando criou a RSE
- RSA 2048bits (Mínimo) ou 4096bits

Cadeia de Certificados

- O arquivo de certificado intermédio (se houver) que foi fornecido pela sua AC
- O certificado de AC que emitiu o certificado do servidor deve ser o primeiro no ficheiro, seguido por quaisquer outros até à raiz. 
- Pode incluir atributos Bag.

Frase-passe

- 1 chave suportada
- Configuração ao importar o certificado

Os certificados com outros parâmetros podem funcionar, mas não podem ser suportados pela Microsoft.

#### <a name="encryption-key-artifacts"></a>Artefactos chave de encriptação

**.pem – Arquivo do Contentor de Certificados**

O nome é do Privacy Enhanced Mail (PEM), um método histórico para o e-mail seguro, mas o formato do recipiente que usou continua a viver, e é uma tradução base64 das teclas X509 ASN.1.  

Definido nos RFCs 1421 a 1424: um formato de contentor que pode incluir apenas o certificado público (como com as instalações apaches, e os ficheiros de certificados ca /etc/ssl/certs), ou pode incluir toda uma cadeia de certificados, incluindo chaves públicas, chave privada e certificados de raiz.  

Também pode codificar um CSR, uma vez que o formato PKCS10 pode ser traduzido para PEM.

**.cert .cer .crt – Arquivo do Contentor de Certificado**

Um ficheiro formatado .pem (ou raramente .der) com uma extensão diferente. É reconhecido pelo Windows Explorer como um certificado. O ficheiro .pem não é reconhecido pelo Windows Explorer.

**.key - Arquivo chave privado**

Um ficheiro KEY é o mesmo "formato" que um ficheiro PEM, mas tem uma extensão diferente.
##### <a name="use-cli-commands-to-deploy-certificates"></a>Utilize comandos CLI para implantar certificados

Utilize o comando *CLI de importação de certificados cyberx-xsense* para importar certificados. Para utilizar esta ferramenta, os ficheiros de certificados precisam de ser carregados para o dispositivo (utilizando ferramentas como winscp ou wget).

O comando suporta as seguintes bandeiras de entrada:

-h Mostrar a linha de comando ajudar a sintaxe

--crt Caminho para o ficheiro de certificado (extensão CRT)

---key *.key ficheiro, o comprimento da chave deve ser mínimo 2048 bits

--cadeia Caminho para o arquivo da cadeia de certificados (opcional)

--passe Palavra-passe usado para encriptar o certificado (opcional)

--passphrase-set Padrão = Falso, não-usado. Definir para TRUE para utilizar a frase de passe anterior fornecida com certificado anterior (opcional)

Ao utilizar o comando CLI:

- Verifique se os ficheiros do certificado são legíveis no aparelho.

- Verifique se o nome de domínio e o IP no certificado correspondem à configuração planeada pelo departamento de TI.


## <a name="connect-a-sensor-to-the-management-console"></a>Ligue um sensor à consola de gestão

Esta secção descreve como garantir a ligação entre o sensor e a consola de gestão no local. Tens de o fazer se estiveres a trabalhar numa rede com lacunas de ar e quiseres enviar informações de ativos e alerta para a consola de gestão a partir do sensor. Esta ligação também permite que a consola de gestão empurre as definições do sistema para o sensor e execute outras tarefas de gestão no sensor.

Para ligar:

1. Inscreva-se na consola de gestão no local.

2. Selecione **Definições do sistema**.

3. Na **secção De Configuração do Sensor – Cadeia de Ligação,** copie a cadeia de ligação gerada automaticamente.

   :::image type="content" source="media/how-to-manage-individual-sensors/connection-string-screen.png" alt-text="Copie a cadeia de ligação deste ecrã."::: 

4. Inscreva-se na consola do sensor.

5. No painel esquerdo, selecione **Definições do Sistema**.

6. Selecione **Ligação do consola de gestão**.

    :::image type="content" source="media/how-to-manage-individual-sensors/management-console-connection-screen.png" alt-text="Screenshot da caixa de diálogo de ligação à consola de gestão.":::

7. Cole a cadeia de ligação na caixa **de cordas Connection** e selecione **Connect**.

8. Na consola de gestão no local, na janela **De Gestão** do Site, atribua o sensor a uma zona.

## <a name="change-the-name-of-a-sensor"></a>Alterar o nome de um sensor

Pode alterar o nome da consola do sensor. O novo nome aparecerá no navegador web da consola, em várias janelas de consolas e em registos de resolução de problemas.

O processo de alteração de nomes de sensores varia para sensores ligados localmente e sensores ligados à nuvem. O nome predefinido é **sensor.**

### <a name="change-the-name-of-a-locally-connected-sensor"></a>Alterar o nome de um sensor ligado localmente

Para alterar o nome:

1. Na parte inferior do painel esquerdo da consola, selecione a etiqueta do sensor atual.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/label-name.png" alt-text="Screenshot que mostra a etiqueta do sensor.":::

1. Na caixa de diálogo do nome do **sensor Editar,** insira um nome.

1. Selecione **Save** (Guardar). O novo nome é aplicado.

### <a name="change-the-name-of-a-cloud-connected-sensor"></a>Mude o nome de um sensor ligado à nuvem

Se o seu sensor foi registado como um sensor ligado à nuvem, o nome do sensor é definido pelo nome atribuído durante o registo. O nome está incluído no ficheiro de ativação que fez o upload ao iniciar sessão pela primeira vez. Para alterar o nome do sensor, é necessário carregar um novo ficheiro de ativação.

Para alterar o nome:

1. No portal Azure Defender for IoT, aceda à página **de Gestão de Sensores.**

1. Elimine o sensor da janela **de Gestão de Sensores.**

1. Registe-se com o novo nome.

1. Descarregue e novo ficheiro de ativação.

1. Inscreva-se no sensor e carre faça o upload do novo ficheiro de ativação.

## <a name="update-the-sensor-network-configuration"></a>Atualizar a configuração da rede de sensores

A configuração da rede do sensor foi definida durante a instalação do sensor. Pode alterar parâmetros de configuração. Também pode configurar uma configuração de procuração.

Se criar um novo endereço IP, poderá ser obrigado a iniciar novamente a sua inscrição.

Para alterar a configuração:

1. No menu lateral, selecione **Definições do Sistema**.

2. Na janela **Definições** do Sistema, selecione **'Rede'.**

    :::image type="content" source="media/how-to-manage-individual-sensors/edit-network-configuration-screen.png" alt-text="Configure as definições de rede.":::

3. Definir os parâmetros da seguinte forma:

    | Parâmetro | Descrição |
    |--|--|
    | Endereço IP | O endereço IP do sensor |
    | Máscara de sub-rede | O endereço da máscara |
    | Gateway predefinido | O endereço de gateway predefinido |
    | DNS | O endereço do servidor DNS |
    | Hostname (Nome do anfitrião) | O nome de hospedeiro do sensor |
    | Proxy | Hospedeiro proxy e nome do porto |

4. Selecione **Save** (Guardar).

## <a name="synchronize-time-zones-on-the-sensor"></a>Sincronizar os fusos horários no sensor

Pode configurar o tempo e a região do sensor para que todos os utilizadores vejam a mesma hora e região.

:::image type="content" source="media/how-to-manage-individual-sensors/time-and-region.png" alt-text="Configurar o tempo e a região.":::

| Parâmetro | Descrição |
|--|--|
| Fuso horário | A definição do fuso horário para:<br />- Alertas<br />- Tendências e widgets estatísticos<br />- Relatórios de extração de dados<br />   -Relatórios de avaliação de riscos<br />- Vetores de ataque |
| Formato de data | Selecione uma das seguintes opções de formato:<br />- dd/MM/yyyy HH:mm:ss<br />- MM/dd/yyyy HH:mm:ss<br />- yyy/MM/dd HH:mm:ss |
| Data e hora | Apresenta a data atual e a hora local no formato que selecionou.<br />Por exemplo, se a sua localização real é América e Nova Iorque, mas o fuso horário está definido para a Europa e Berlim, o tempo é exibido de acordo com a hora de Berlim. |

Para configurar o tempo do sensor:

1. No menu lateral, selecione **Definições do Sistema**.

2. Na janela Definições do **Sistema,** selecione **Tempo & Regional**.

3. Desacione os parâmetros e **selecione Guardar**.

## <a name="set-up-backup-and-restore-files"></a>Configurar ficheiros de backup e restauro

A cópia de segurança do sistema é executada automaticamente às 3:00 da manhã diária. Os dados são guardados num disco diferente no sensor. A localização predefinida é `/var/cyberx/backups` .

Pode transferir automaticamente este ficheiro para a rede interna.

> [!NOTE]
> - O procedimento de backup e restauro pode ser realizado apenas entre as mesmas versões.
> - Em algumas arquiteturas, o backup é deficiente. Pode ative-lo no `/var/cyberx/properties/backup.properties` ficheiro.

Quando controla um sensor utilizando a consola de gestão no local, pode utilizar o horário de backup do sensor para recolher estas cópias de segurança e armazená-las na consola de gestão ou num servidor externo de backup.

**O que está apoiado:** Configurações e dados.

**O que não está apoiado:** Ficheiros e registos PCAP. Pode fazer o back-up manualmente e restaurar PCAPs e registos.

Os ficheiros de cópia de segurança do sensor são automaticamente nomeados através do seguinte formato: `<sensor name>-backup-version-<version>-<date>.tar` . Um exemplo é `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`.

Para configurar a cópia de segurança:

- Inscreva-se numa conta administrativa e `$ sudo cyberx-xsense-system-backup` entre.

Para restaurar o mais recente ficheiro de backup:

- Inscreva-se numa conta administrativa e `$ sudo cyberx-xsense-system-restore` entre.

Para guardar a cópia de segurança para um servidor SMB externo:

1. Crie uma pasta partilhada no servidor SMB externo.

    Obtenha o caminho da pasta, nome de utilizador e senha necessária para aceder ao servidor SMB.

2. No sensor, faça um diretório para as cópias de segurança:

    - `sudo mkdir /<backup_folder_name_on_cyberx_server>`

    - `sudo chmod 777 /<backup_folder_name_on_cyberx_server>/`

3. `fstab`Editar: 

    - `sudo nano /etc/fstab`

    - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifsrw,credentials=/etc/samba/user,vers=X.X,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0`

4. Editar e criar credenciais para partilhar para o servidor SMB:

    `sudo nano /etc/samba/user` 

5. Adicionar:

    - `username=&gt:user name&lt:`

    - `password=<password>`

6. Monte o diretório:

    `sudo mount -a`

7. Configure um diretório de reserva para a pasta partilhada no sensor Defender para IoT:  

    - `sudo nano /var/cyberx/properties/backup.properties`

    - `set backup_directory_path to <backup_folder_name_on_cyberx_server>`

### <a name="restore-sensors"></a>Restaurar sensores

Pode restaurar as cópias de segurança da consola do sensor e utilizando o CLI.

**Para restaurar a partir da consola:**

- Selecione **Restaurar a imagem** a partir da janela de **Definições** do Sistema do sensor.

:::image type="content" source="media/how-to-manage-individual-sensors/restore-image-screen.png" alt-text="Restaurar a sua imagem selecionando o botão.":::

A consola apresentará falhas de restauro.

**Para restaurar utilizando o CLI:**

- Inscreva-se numa conta administrativa e `$ sudo cyberx-management-system-restore` entre.

## <a name="update-a-standalone-sensor-version"></a>Atualize uma versão de sensor autónomo

O procedimento a seguir descreve como atualizar um sensor autónomo utilizando a consola sensor. O processo de atualização demora cerca de 30 minutos.

1. Aceda ao [portal do Azure](https://portal.azure.com/).

2. Vá ao Defender para ioT.

3. Aceda à página **'Atualizações'.**

   :::image type="content" source="media/how-to-manage-individual-sensors/updates-page.png" alt-text="Screenshot da página de Atualizações do Defender para IoT.":::

4. Selecione **Descarregar** na secção **Sensores** e guardar o ficheiro.

5. Na barra lateral da consola do sensor, selecione **Definições do Sistema**.

6. No painel **de atualização** da versão, selecione **Upgrade**.

    :::image type="content" source="media/how-to-manage-individual-sensors/upgrade-pane-v2.png" alt-text="Screenshot do painel de upgrade.":::

7. Selecione o ficheiro que descarregou na página Defender para **IoT Updates.**

8. O processo de atualização inicia-se, período durante o qual o sistema é reiniciado duas vezes. Após o primeiro reboot (antes da conclusão do processo de atualização), o sistema abre-se com a janela de inscrição. Depois de iniciar sinsent, a versão de atualização aparece na parte inferior esquerda da barra lateral.

    :::image type="content" source="media/how-to-manage-individual-sensors/defender-for-iot-version.png" alt-text="Screenshot da versão de upgrade que aparece depois de iniciar sinduções.":::

## <a name="forward-sensor-failure-alerts"></a>Alertas de falha do sensor para a frente 

Pode encaminhar alertas para terceiros para fornecer detalhes sobre:

- Sensores desligados

- Falhas remotas de backup

Estas informações são enviadas quando cria uma regra de encaminhamento para notificações do sistema.

> [!NOTE]
> Os administradores podem enviar notificações do sistema.

Para enviar notificações:

1. Inscreva-se na consola de gestão no local.
1. Selecione **Encaminhamento** do menu lateral.
1. Criar uma regra de encaminhamento.
1. Selecione **notificações do sistema de relatórios**.

Para obter mais informações sobre as regras de encaminhamento, consulte [as informações de alerta do Avante](how-to-forward-alert-information-to-partners.md).

## <a name="adjust-system-properties"></a>Ajustar as propriedades do sistema

As propriedades do sistema controlam várias operações e configurações no sensor. Editá-los ou modificá-los pode danificar o funcionamento da consola do sensor.

Consulte o [Microsoft Support](https://support.microsoft.com/) antes de alterar as suas definições.

Para aceder às propriedades do sistema:

1. Inscreva-se na consola de gestão no local ou no sensor.

2. Selecione **Definições do sistema**.

3. Selecione Propriedades do **Sistema** na secção **Geral.**

## <a name="see-also"></a>Ver também

[Pesquisa e pacotes de inteligência de ameaça](how-to-work-with-threat-intelligence-packages.md)

[Gerir sensores a partir da consola de gestão](how-to-manage-sensors-from-the-on-premises-management-console.md)
