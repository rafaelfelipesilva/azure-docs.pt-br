---
title: Ocorrência de um erro interno ao fazer uma conexão RDP com as Máquinas Virtuais do Microsoft Azure | Microsoft Docs
description: Saiba como solucionar problemas de erros internos de RDP no Microsoft Azure | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/22/2018
ms.author: genli
ms.openlocfilehash: 4476e4732dfcf8d79c9678a7ff4719eba10e48f3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60319421"
---
#  <a name="an-internal-error-occurs-when-you-try-to-connect-to-an-azure-vm-through-remote-desktop"></a>Ocorre um erro interno ao tentar se conectar a uma VM do Azure por meio da área de trabalho remota

Este artigo descreve um erro que você pode enfrentar ao tentar se conectar a uma VM (máquina virtual) do Windows no Microsoft Azure.
> [!NOTE]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo cobre o uso do modelo de implantação do Gerenciador de Recursos, quais recomendamos usar para novas implantações em vez do modelo de implantação clássico.

## <a name="symptoms"></a>Sintomas

Você não pode se conectar a uma VM do Azure usando o RDP (protocolo de área de trabalho remota). A conexão fica presa na seção “Configuração remota” ou você recebe a seguinte mensagem de erro:

- Erro Interno do RDP
- Ocorreu um erro interno
- Esse computador não pode ser conectado ao computador remoto. Tente se conectar novamente. Se o problema persistir, entre em contato com o proprietário do computador remoto ou o administrador da rede


## <a name="cause"></a>Causa

Esse problema pode ocorrer pelos seguintes motivos:

- As chaves de criptografia RSA locais não podem ser acessadas.
- O protocolo TLS está desabilitado.
- O certificado está corrompido ou expirado.

## <a name="solution"></a>Solução

Antes de seguir essas etapas, tire um instantâneo do disco do SO da VM afetada como um backup. Para obter mais informações, consulte [Instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).

Para solucionar esse problema, use o Console serial ou [repare a VM off-line](#repair-the-vm-offline) anexando o disco do SO da VM a uma VM de recuperação.


### <a name="use-serial-control"></a>Usar o Controle serial

Conectar-se ao [Console Serial e abrir uma instância do PowerShell](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Se o Console Serial não estiver habilitado na sua VM, vá até a seção [reparar a VM offline](#repair-the-vm-offline).

#### <a name="step-1-check-the-rdp-port"></a>Etapa: 1: verificar a porta do RDP

1. Em uma instância do PowerShell, use [NETSTAT](https://docs.microsoft.com/windows-server/administration/windows-commands/netstat
) para verificar se a porta 8080 é usada por outros aplicativos:

        Netstat -anob |more
2. Se Termservice.exe estiver usando a porta 8080, vá para a etapa 2. Se outro serviço ou aplicativo que não seja o Termservice.exe estiver usando a porta 8080, siga estas etapas:

    1. Interrompa o serviço para o aplicativo que está usando o serviço 3389:

            Stop-Service -Name <ServiceName> -Force

    2. Iniciar o serviço terminal:

            Start-Service -Name Termservice

2. Se o aplicativo não puder ser interrompido ou se esse método não se aplicar a você, altere a porta para RDP:

    1. Altere a porta:

            Set-ItemProperty -Path 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name PortNumber -value <Hexportnumber>

            Stop-Service -Name Termservice -Force
            
            Start-Service -Name Termservice 

    2. Configure o firewall para a nova porta:

            Set-NetFirewallRule -Name "RemoteDesktop-UserMode-In-TCP" -LocalPort <NEW PORT (decimal)>

    3. [Atualize o grupo de segurança de rede para a nova porta](../../virtual-network/security-overview.md) na porta de RDP do portal do Azure.

#### <a name="step-2-set-correct-permissions-on-the-rdp-self-signed-certificate"></a>Etapa 2: Definir as permissões corretas no certificado autoassinado RDP

1.  Em uma instância do PowerShell, execute os comandos a seguir individualmente para renovar o certificado autoassinado do RDP:

        Import-Module PKI 
    
        Set-Location Cert:\LocalMachine 
        
        $RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint) 
        
        Remove-Item -Path $RdpCertThumbprint

        Stop-Service -Name "SessionEnv"

        Start-Service -Name "SessionEnv"

2. Se você não puder renovar o certificado usando esse método, tente renovar o certificado autoassinado do RDP remotamente:

    1. Em uma VM que tenha conectividade com a VM de trabalho que está com problemas, digite **mmc** na caixa **Executar** para abrir o Console de Gerenciamento Microsoft.
    2. No menu **Arquivo**, selecione **Adicionar/Remover Snap-in**, depois **Certificados** e depois **Adicionar**.
    3. Selecione **Contas do computador**, depois **Outro computador** e adicione o endereço IP da VM com problemas.
    4. Vá até a pasta **Área de Trabalho Remota\Certificados**, clique com o botão direito do mouse no certificado e selecione **Excluir**.
    5. Em uma instância do PowerShell a partir do Console Serial, reinicie o serviço de Configuração de Área de Trabalho Remota:

            Stop-Service -Name "SessionEnv"

            Start-Service -Name "SessionEnv"
3. Redefina a permissão para a pasta MachineKeys.

        remove-module psreadline icacls

        md c:\temp

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt 
        
        takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt 
        
        Restart-Service TermService -Force

4. Reinicie a VM, depois tente Iniciar uma conexão de Área de Trabalho Remota à VM. Se o erro ainda ocorrer, vá para a próxima etapa.

Etapa 3: Habilitar todas as versões com suporte do TLS

O cliente RDP usa o TLS 1.0 como o protocolo padrão. No entanto, ele pode ser alterado para TLS 1.1, que se tornou o novo padrão. Se o TLS 1.1 estiver desabilitado na VM, a conexão falhará.
1.  Em uma instância CMD, habilite o protocolo TLS:

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
2.  Para impedir que a política do AD substitua as alterações, interrompa temporariamente a atualização de política de grupo:

        REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
3.  Reinicie a VM para que as alterações entrem em vigor. Se o problema for resolvido, execute o seguinte comando para habilitar novamente a política de grupo:

        sc config gpsvc start= auto sc start gpsvc

        gpupdate /force
    Se a alteração for revertida, isso significa que há uma política do Active Directory no domínio da sua empresa. Você precisa alterá-la para evitar que esse problema ocorra novamente.

### <a name="repair-the-vm-offline"></a>Reparar a VM Offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Anexar o disco de SO a uma VM de recuperação

1. [Anexar o disco de SO a uma VM de recuperação](../windows/troubleshoot-recovery-disks-portal.md).
2. Depois que o disco do sistema operacional é anexado à VM de recuperação, verifique se o disco está sinalizado como **on-line** no console de gerenciamento de disco. Observe a letra da unidade atribuída ao disco de SO anexado.
3. Inicie uma conexão de área de trabalho remota para a VM de recuperação.

#### <a name="enable-dump-log-and-serial-console"></a>Habilitar o log de despejo e o Console Serial

Para habilitar o log de despejo e o Console Serial, execute o script a seguir.

1. Abra uma sessão de prompt de comandos com privilégios elevados (**Executar como administrador**).
2. Execute o seguinte script:

    Nesse script, presumimos que a letra da unidade atribuída ao disco do SO anexado é F. Substitua essa letra da unidade pelo valor apropriado para a VM.

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

#### <a name="reset-the-permission-for-machinekeys-folder"></a>Redefinir a permissão para a pasta MachineKeys

1. Abra uma sessão de prompt de comandos com privilégios elevados (**Executar como administrador**).
2. Execute o seguinte script. Nesse script, presumimos que a letra da unidade atribuída ao disco do SO anexado é F. Substitua essa letra da unidade pelo valor apropriado para a VM.

        Md F:\temp

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
        
        takeown /f "F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt

#### <a name="enable-all-supported-tls-versions"></a>Habilitar todas as versões com suporte do TLS

1.  Abra uma sessão de prompt de comandos com privilégios elevados (**Executar como administrador**), depois execute os comandos a seguir. O script a seguir presume que a letra da unidade que é atribuída ao disco de SO anexado é F. Substitua essa letra da unidade pelo valor apropriado para a VM.
2.  Verifique qual TLS está habilitado:

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWO

3.  Se a chave não existir ou seu valor for **0**, habilite o protocolo executando os scripts a seguir:

        REM Enable TLS 1.0, TLS 1.1 and TLS 1.2

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

4.  Habilite o NLA:

        REM Enable NLA

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f reg unload HKLM\BROKENSYSTEM
5.  [Desanexe o disco de SO e recrie a VM](../windows/troubleshoot-recovery-disks-portal.md), depois verifique se o problema for resolvido.





