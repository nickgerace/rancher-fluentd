FROM mcr.microsoft.com/windows/servercore:2004

SHELL ["powershell", "-NoLogo", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

RUN Set-ExecutionPolicy Bypass -Scope Process -Force; \
    iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))

RUN choco install -y ruby --version 2.4.2.2 --params "'/InstallDir:C:\'"; \
    choco install -y msys2 --params "'/NoPath /NoUpdate /InstallDir:C:\ruby24\msys64'"

COPY Gemfile 'C:\fluentd\install\Gemfile'
COPY Gemfile.lock 'C:\fluentd\install\Gemfile.lock'

RUN refreshenv; ridk install 2 3
RUN 'gem: --no-document' | Out-File -Encoding UTF8 -NoNewline -Force -FilePath 'C:\ProgramData\gemrc'
RUN gem install bundler
RUN bundle config build.certstore_c --with-cflags="-Wno-attributes"
RUN bundle config build.yajl-ruby --with-cflags="-Wno-attributes"
RUN bundle config build.oj --with-cflags="-Wno-attributes"
RUN bundle install --gemfile='C:\fluentd\install\Gemfile'

RUN gem sources --clear-all; \
Remove-Item -Force C:\ruby27\lib\ruby\gems\2.7.0\cache\*.gem; \
Remove-Item -Recurse -Force C:\ProgramData\chocolatey

COPY upgrade-gems.ps1 'C:\fluentd\install\upgrade-gems.ps1'
COPY fluent.conf 'C:\etc\fluent\fluent.conf'

CMD ["powershell", "-command", "fluentd"]
