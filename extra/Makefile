# makefile for flatpak

build: 
	(cd src/content && yarn --offline --ignore-engines)
	(cd src/BloomBrowserUI && yarn --offline --ignore-engines)
	(cd src/BloomBrowserUI && yarn --offline --ignore-engines build)
	msbuild /t:SetAssemblyVersion /p:RootDir=`pwd` /p:BUILD_NUMBER=${FULL_BUILD_NUMBER} build/Bloom.proj
	msbuild /p:Configuration=Release /p:BUILD_NUMBER=${FULL_BUILD_NUMBER} /v:diag "Bloom.sln"
	msbuild /p:Configuration=Release /p:BUILD_NUMBER=${FULL_BUILD_NUMBER} src/LinuxBloomLauncher/LinuxBloomLauncher.cproj

##################################################################
# mimic the installation steps used by the debian packaging
install:
	# Install library files
	install -d ${BLOOMLIB}
	cp -rv output/Release/* ${BLOOMLIB}
	find ${BLOOMLIB} -type f -print0 | xargs -0 chmod 644
	chmod +x ${BLOOMLIB}/BloomLauncher
	chmod +x ${BLOOMLIB}/*.sh
	chmod +x ${BLOOMLIB}/*.exe
	chmod +x ${BLOOMLIB}/*.dll
	chmod +x ${BLOOMLIB}/Firefox/*.so*
	find ${BLOOMLIB} -type d -print0 | xargs -0 chmod 755
	install -d ${BLOOMSHARE}
	install -m 644 environ* ${BLOOMSHARE}
	# Install data files
	cp -r DistFiles/* ${BLOOMLIB}
	chmod -R a+rX,og-w ${BLOOMLIB}
	# Install browser support files
	cp -r output/browser ${BLOOMLIB}
	chmod -R a+rX,og-w ${BLOOMLIB}/browser
	# Install wrapper script
	install -d ${INSTALLATION_PREFIX}/bin
	install debian/bloom ${INSTALLATION_PREFIX}/bin
	# Add to Applications menu
	install -d ${INSTALLATION_PREFIX}/share/pixmaps
	install -d ${INSTALLATION_PREFIX}/share/icons/hicolor/48x48/mimetypes/
	install -d ${INSTALLATION_PREFIX}/share/icons/hicolor/scalable/mimetypes/
	install -m 644 debian/bloom.png ${INSTALLATION_PREFIX}/share/pixmaps/org.sil.Bloom.png
	install -m 644 debian/bloom.svg ${INSTALLATION_PREFIX}/share/pixmaps/org.sil.Bloom.svg
	install -m 644 debian/bloom.png ${INSTALLATION_PREFIX}/share/icons/hicolor/48x48/mimetypes/org.sil.Bloom.png
	install -m 644 debian/bloom.svg ${INSTALLATION_PREFIX}/share/icons/hicolor/scalable/mimetypes/org.sil.Bloom.svg
	install -d ${INSTALLATION_PREFIX}/share/applications
	desktop-file-install --dir ${INSTALLATION_PREFIX}/share/applications --set-icon=org.sil.Bloom debian/bloom.desktop
	# Install icon for our mime type so that it shows up as icon for a bloompack
	install -m 644 debian/BloomPack.png ${INSTALLATION_PREFIX}/share/icons/hicolor/48x48/mimetypes/application-bloom.png
	install -m 644 debian/BloomPack.svg ${INSTALLATION_PREFIX}/share/icons/hicolor/scalable/mimetypes/application-bloom.svg
	# Add bloom-collection mime type
	install -m 644 debian/bloom-collection.png ${INSTALLATION_PREFIX}/share/icons/hicolor/48x48/mimetypes/application-bloom-collection.png
	install -m 644 debian/bloom-collection.svg ${INSTALLATION_PREFIX}/share/icons/hicolor/scalable/mimetypes/application-bloom-collection.svg
	# Add bloom-join-team mime type
	install -m 644 debian/JoinBloomTC.png ${INSTALLATION_PREFIX}/share/icons/hicolor/48x48/mimetypes/application-bloom-join-team.png
	install -m 644 debian/JoinBloomTC.svg ${INSTALLATION_PREFIX}/share/icons/hicolor/scalable/mimetypes/application-bloom-join-team.svg
	# Add AppStream metadata file
	install -d ${INSTALLATION_PREFIX}/share/appdata
	install -m 644 debian/bloom.appdata.xml ${INSTALLATION_PREFIX}/share/appdata
	# allow GTK2 to co-exist with mono-sil and Bloom
	ln -s ${BLOOMLIB}/Firefox/libgtk-x11-2.0.so.0 /app/lib/mono/gac/System.Windows.Forms/4.0.0.0__b77a5c561934e089
	# Remove unwanted files
	cd ${BLOOMLIB} && rm -rf AxInterop* BloomTests* Interop* Moq* nunit* NUnit* AndikaNewBasic ffmpeg ghostscript Microsoft.WindowsAPICodePack*
	find ${BLOOMLIB} -name '*.map' -print0 | xargs -0 rm -f
	find ${BLOOMLIB} -name '*Spec.js' -print0 | xargs -0 rm -f
	find ${BLOOMLIB} -name 'CheckOrFixXliff.*' -print0 | xargs -0 rm -f
	find ${BLOOMLIB} -name 'GeckofxHtmlToPdf.*' -print0 | xargs -0 rm -f
	find ${BLOOMLIB} -name '*UnitTestFramework.dll' -type d -print0 | xargs -0 rm -rf
	find ${BLOOMLIB} -name 'Moq.*' -print0 | xargs -0 rm -f
	# We need NAudio.dll for SIL.Media to load even though it's Windows-only.  But we don't need the xml file.
	find ${BLOOMLIB} -name 'NAudio.xml' -print0 | xargs -0 rm -f
	find ${BLOOMLIB} -name 'irrKlang.NET4.dll' -print0 | xargs -0 rm -f
	find ${BLOOMLIB} -name 'win-x86' -type d -print0 | xargs -0 rm -rf
	find ${BLOOMLIB} -name 'win-x64' -type d -print0 | xargs -0 rm -rf
	find ${BLOOMLIB} -name '*Interop.AcroPDFLib.*' -print0 | xargs -0 rm -f
	# These are needed to compile libpalaso on Windows, but aren't really used even there I suspect.
	find ${BLOOMLIB} -name 'AtkSharp.dll' -print0 | xargs -0 rm -f
	find ${BLOOMLIB} -name 'CairoSharp.dll' -print0 | xargs -0 rm -f
	find ${BLOOMLIB} -name 'GdkSharp.dll' -print0 | xargs -0 rm -f
	find ${BLOOMLIB} -name 'GioSharp.dll' -print0 | xargs -0 rm -f
	find ${BLOOMLIB} -name 'GLibSharp.dll' -print0 | xargs -0 rm -f
	find ${BLOOMLIB} -name 'GtkSharp.dll' -print0 | xargs -0 rm -f
	find ${BLOOMLIB} -name 'PangoSharp.dll' -print0 | xargs -0 rm -f
	# make up for deficient mono mapping
	ln -s /usr/lib/x86_64-linux-gnu/libdl.so.2 ${INSTALLATION_PREFIX}/lib/libdl.so
	# ensure that "python" points to /usr/bin/python3
	rm -f /app/bin/python
