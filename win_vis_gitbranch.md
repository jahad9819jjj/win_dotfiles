
Powershellで以下を開く
```
notepad $PROFILE
```

`Microsoft.PowerShell_profile.ps1`が開かれるはずなので開かれたら以下をコピー

```
function Write-BranchName () {
    try {
        $branch = git rev-parse --abbrev-ref HEAD

        if ($branch -eq "HEAD") {
            # we're probably in detached HEAD state, so print the SHA
            $branch = git rev-parse --short HEAD
            Write-Host " ($branch)" -ForegroundColor "red"
        }
        else {
            # we're on an actual branch, so print it
            Write-Host " ($branch)" -ForegroundColor "blue"
        }
    } catch {
        # we'll end up here if we're in a newly initiated git repo
        Write-Host " (no branches yet)" -ForegroundColor "yellow"
    }
}

function prompt {
    $base = "PS "
    $path = "$($executionContext.SessionState.Path.CurrentLocation)"
    $userPrompt = "$('>' * ($nestedPromptLevel + 1)) "

    Write-Host "`n$base" -NoNewline

    if (Test-Path .git) {
        Write-Host $path -NoNewline -ForegroundColor "green"
        Write-BranchName
    }
    else {
        # we're not in a repo so don't bother displaying branch name/sha
        Write-Host $path -ForegroundColor "green"
    }

    return $userPrompt
}
```

そのあと、以下の設定をする必要がある
管理者権限でPowershellを開く
=>
```
Set-ExecutionPolicy RemoteSigned
```

意味：
~
Set-ExecutionPolicy RemoteSignedは、PowerShellの実行ポリシーを変更するためのコマンドです。実行ポリシーは、PowerShellスクリプトの実行を許可するかどうかを制御します。RemoteSignedは、ローカルコンピューター上で作成されたスクリプトを実行することができますが、信頼されていない場所からダウンロードされたスクリプトは署名されている必要があります
PowerShellのデフォルトの実行ポリシーは、Windowsクライアントの場合はRestricted、Windowsサーバーの場合はRemoteSignedです。Restrictedの場合、すべてのスクリプトが実行禁止になっています。RemoteSignedの場合、Windowsサーバーのコンピューターでスクリプトを実行できますが、信頼されていない場所からダウンロードされたスクリプトは署名されている必要があります
~