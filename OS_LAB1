#include <windows.h>
#include <iostream>
#include <string.h>

using namespace std;

int completions = 0;

void DiskList();
void DiskInfo();
void CreateDeleteDirectory();
void CreateFileInDirectory();
void CopyMoveFile();
void FileAttributes();
void NormalizeOutputString(char* begin, char* end);
void FileAttrOut(DWORD fileAttr);
void CopyFileTask();
void Completion(DWORD dwErrorCode, DWORD dwNumberOfBytesTransfered, LPOVERLAPPED lpOverlapped) { ++completions; }

class Menu {
private:
	static int chosen;
	static bool work;
	static void execute(int n) {
		if (!work) return;
		switch (n)
		{
		case 1: DiskList();
			break;
		case 2:
			DiskInfo();
			break;
		case 3:
			CreateDeleteDirectory();
			break;
		case 4:
			CreateFileInDirectory();
			break;
		case 5:
			CopyMoveFile();
			break;
		case 6:
			FileAttributes();
			break;
		case 7:
			CopyFileTask();
			break;
		default:
			break;
		}
	}
public:
	static void init() { chosen = 0; work = true; }
	static void PrintList() {
		printf(
			"1 - âûâîä ñïèñêà äèñêîâ\n"
			"2 - âûâîä èíôîðìàöèè î äèñêå è ðàçìåð ñâîáîäíîãî ïðîñòðàíñòâà\n"
			"3 - ñîçäàíèå è óäàëåíèå çàäàííûõ êàòàëîãîâ\n"
			"4 - ñîçäàíèå ôàéëîâ â íîâûõ êàòàëîãàõ\n"
			"5 - êîïèðîâàíèå è ïåðåìåùåíèå ôàéëîâ ìåæäó êàòàëîãàìè\n"
			"6 - àíàëèç è èçìåíåíèå àòðèáóòîâ ôàéëîâ\n"
			"7 - êîïèðîâàíèå ôàéëà ñ ïîìîùüþ îïåðàöèé ïåðåêðûâàþùåãîñÿ ââîäà-âûâîäà\n"
			"0 - âûõîä èç ïðîãðàììû\n");
	}
	static void setChosen(int value) { chosen = value; work = value == 0 ? false : true; execute(value); }
	static bool getWork() { return work; }
};
int Menu::chosen;
bool Menu::work;

int main()
{
	string tmp;
	int val;
	Menu::init();
	SetConsoleCP(1251);
	SetConsoleOutputCP(1251);
	// menu loop
	while (Menu::getWork()) {
		system("cls");
		Menu::PrintList();
		cin >> tmp;
		val = _strtoi64(tmp.c_str(), nullptr, 10);
		Menu::setChosen(val);
		printf("Íàæìèòå ëþáóþ êíîïêó...");
		getchar();
		getchar();
	}

	return 0;
}
// deletes extra '\0' symbols from string ("N\0T\0F\0S\0\0\0" -> "NTFS\0") 
void NormalizeOutputString(char* begin, char* end) {
	bool stop = false;
	char tmp[100];
	int i = 0;
	for (char* ptr = begin; ptr != end && !stop; ++ptr) {
		if (*ptr == '\0') {
			if (*(ptr + 1) == '\0') stop = true;
		}
		else {
			tmp[i++] = *ptr;
		}
	}
	tmp[i] = '\0';
	for (char* ptr = begin, i = 0; ptr != end; ++ptr, ++i) {
		*ptr = tmp[i];
		if (tmp[i] == '\0') break;
	}
}
// reads file attributes
void FileAttrOut(DWORD fileAttr) {
	if (fileAttr & FILE_ATTRIBUTE_ARCHIVE)
		printf("Àðõèâíûé ôàéë èëè êàòàëîã\n");
	if (fileAttr & FILE_ATTRIBUTE_COMPRESSED)
		printf("Ñæàòûé ôàéë èëè êàòàëîã\n");
	if (fileAttr & FILE_ATTRIBUTE_DIRECTORY)
		printf("Êàòàëîã\n");
	if (fileAttr & FILE_ATTRIBUTE_ENCRYPTED)
		printf("Çàøèôðîâàííûé ôàéë èëè êàòàëîã\n");
	if (fileAttr & FILE_ATTRIBUTE_HIDDEN)
		printf("Ôàéë èëè êàòàëîã ñêðûò\n");
	if (fileAttr & FILE_ATTRIBUTE_INTEGRITY_STREAM)
		printf("Êàòàëîã èëè ïîòîê äàííûõ ïîëüçîâàòåëÿ íàñòðàèâàåòñÿ ñ öåëîñòíîñòüþ (ïîääåðæèâàåòñÿ òîëüêî â òîìàõ ReFS)\n");
	if (fileAttr & FILE_ATTRIBUTE_NORMAL)
		printf("Ôàéë, êîòîðûé íå èìååò äðóãèõ àòðèáóòîâ\n");
	if (fileAttr & FILE_ATTRIBUTE_NOT_CONTENT_INDEXED)
		printf("Ôàéë èëè êàòàëîã íå èíäåêñèðóþòñÿ ñëóæáîé èíäåêñèðîâàíèÿ ñîäåðæèìîãî\n");
	if (fileAttr & FILE_ATTRIBUTE_NO_SCRUB_DATA)
		printf("Ïîòîê äàííûõ ïîëüçîâàòåëÿ, êîòîðûé íå áóäåò ñ÷èòûâàòüñÿ ñ ïîìîùüþ ñðåäñòâà ïðîâåðêè öåëîñòíîñòè ôîíîâûõ äàííûõ (SCRUBBER AKA)\n");
	if (fileAttr & FILE_ATTRIBUTE_OFFLINE)
		printf("Äàííûå ôàéëà íåäîñòóïíû íåìåäëåííî\n");
	if (fileAttr & FILE_ATTRIBUTE_READONLY)
		printf("Ôàéë, äîñòóïíûé òîëüêî äëÿ ÷òåíèÿ\n");
	if (fileAttr & FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS)
		printf("Ôàéë èëè êàòàëîã íå ïîëíîñòüþ ïðèñóòñòâóåò ëîêàëüíî\n");
	if (fileAttr & FILE_ATTRIBUTE_RECALL_ON_OPEN)
		printf("Ôàéë èëè êàòàëîã íå èìååò ôèçè÷åñêîãî ïðåäñòàâëåíèÿ â ëîêàëüíîé ñèñòåìå; ýëåìåíò ÿâëÿåòñÿ âèðòóàëüíûì\n");
	if (fileAttr & FILE_ATTRIBUTE_REPARSE_POINT)
		printf("Ôàéë èëè êàòàëîã ñ ñâÿçàííîé òî÷êîé ïîâòîðíîãî èçìåðåíèÿ èëè ôàéëîì, êîòîðûé ÿâëÿåòñÿ ñèìâîëüíîé ññûëêîé\n");
	if (fileAttr & FILE_ATTRIBUTE_SPARSE_FILE)
		printf("Ôàéë, êîòîðûé ÿâëÿåòñÿ ðàçðåæåííûì ôàéëîì\n");
	if (fileAttr & FILE_ATTRIBUTE_SYSTEM)
		printf("Ôàéë èëè êàòàëîã, êîòîðûé îïåðàöèîííàÿ ñèñòåìà èñïîëüçóåò ÷àñòè÷íî èëè ïîëíîñòüþ\n");
	if (fileAttr & FILE_ATTRIBUTE_TEMPORARY)
		printf("Ôàéë, èñïîëüçóåìûé äëÿ âðåìåííîãî õðàíèëèùà\n");
	if (fileAttr & FILE_ATTRIBUTE_PINNED)
		printf("Ôàéë èëè êàòàëîã äîëæíû áûòü ïîëíîñòüþ ïðåäñòàâëåíû ëîêàëüíî, äàæå åñëè äîñòóï ê íåé íå âûïîëíÿåòñÿ àêòèâíî\n");
	if (fileAttr & FILE_ATTRIBUTE_UNPINNED)
		printf("Ôàéë èëè êàòàëîã íå äîëæíû õðàíèòüñÿ ëîêàëüíî, çà èñêëþ÷åíèåì ñëó÷àåâ àêòèâíîãî äîñòóïà\n");
}
// GetLogicalDrives, GetLogicalDriveStrings
void DiskList() {
	int val, disks, n;
	string tmp;
	DWORD Ddrives = 255; // Max buffer length
	char lpBuffer[255];
	printf(
		"Ñïèñîê äèñêîâ: \n"
		"1 - èñïîëüçóÿ ôóíêöèþ GetLogicalDrives\n"
		"2 - èñïîëüçóÿ ôóíêöèþ GetLogicalDriveStrings\n");
	cin >> tmp;
	val = _strtoi64(tmp.c_str(), nullptr, 10); // convert string to int
	switch (val)
	{
	case 1:
		disks = GetLogicalDrives();
		// decode answer
		if (disks != 0) {
			for (int i = 0; i < 26; i++) {
				n = ((disks >> i) & 1);
				if (n) cout << "Íàéäåí äèñê " << char(65 + i) << ":\\\n";
			}
		}
		break;
	case 2:
		n = GetLogicalDriveStrings(Ddrives, (LPWSTR)lpBuffer);
		printf("Íàéäåíû äèñêè: ");
		// print answer w/o (int n * 2 represents number of output chars)
		for (int i = 0; i < n * 2; i++)
			printf("%c", lpBuffer[i]);
		printf("\n");
		break;
	default:
		break;
	}
}
// GetDriveType, GetVolumeInformation, GetDiskFreeSpace
void DiskInfo() {
	int val, disk;
	char diskLetter, lpVolumeNameBuffer[100], lpFileSystemNameBuffer[100];
	bool res;
	string tmp;
	wstring wtmp;
	LPCWSTR l;
	DWORD nVolumeNameSize = 100;
	unsigned long lpVolumeSerialNumber, lpMaximumComponentsLength, lpFileSystemFlags,
		lpSectorsPerCluster, lpBytesPerSector, lpNumberOfFreeClusters, lpTotalNumberOfClusters;
	printf(
		"Èíôîðìàöèÿ î äèñêàõ: \n"
		"1 - ïîëó÷èòü òèï äèñêà\n"
		"2 - ïîëó÷èòü èíôîðìàöèþ î ðàçäåëå\n"
		"3 - ïîëó÷èòü èíôîðìàöèþ î ñâîáîäíîì ìåñòå íà äèñêå\n");
	cin >> tmp;
	val = _strtoi64(tmp.c_str(), nullptr, 10); // convert string to int
	printf("Ââåäèòå áóêâó äèñêà: ");
	cin >> diskLetter;
	tmp = diskLetter; // save disk letter
	tmp.append(":\\"); // create path
	// convert string to LPCWSTR
	wtmp = wstring(tmp.begin(), tmp.end());
	l = wtmp.c_str();
	//
	switch (val)
	{
	case 1:
		disk = GetDriveType(l);
		// decode answer
		switch (disk)
		{
		case DRIVE_UNKNOWN:
			printf("UNKNOWN\n");
			break;
		case DRIVE_NO_ROOT_DIR:
			printf("DRIVE NO ROOT DIR\n");
			break;
		case DRIVE_REMOVABLE:
			printf("REMOVABLE\n");
			break;
		case DRIVE_FIXED:
			printf("FIXED\n");
			break;
		case DRIVE_REMOTE:
			printf("REMOTE\n");
			break;
		case DRIVE_CDROM:
			printf("CDROM\n");
			break;
		case DRIVE_RAMDISK:
			printf("RAMDISK\n");
			break;
		default:
			break;
		}
		break;
	case 2:
		res = GetVolumeInformation(l, (LPWSTR)lpVolumeNameBuffer, nVolumeNameSize, &lpVolumeSerialNumber, &lpMaximumComponentsLength, &lpFileSystemFlags, (LPWSTR)lpFileSystemNameBuffer, nVolumeNameSize);
		if (res) {
			// delete extra '\0' symbols
			NormalizeOutputString(begin(lpFileSystemNameBuffer), end(lpFileSystemNameBuffer));
			NormalizeOutputString(begin(lpVolumeNameBuffer), end(lpVolumeNameBuffer));
			//
			printf(
				"Íàçâàíèå ðàçäåëà: %s\n"
				"Èìÿ ôàéëîâîé ñèñòåìû: %s\n"
				"Ñåðèéíûé íîìåð: %u\n"
				"Ìàêñèìàëüíàÿ äëèíà ôàéëà: %u\n"
				"Îïöèè ôàéëîâîé ñèñòåìû: \n",
				lpVolumeNameBuffer,
				lpFileSystemNameBuffer,
				lpVolumeSerialNumber,
				lpMaximumComponentsLength);
			// decode system flags (from microsoft documentation)
			if (lpFileSystemFlags & FILE_CASE_SENSITIVE_SEARCH)
				printf("Óêàçàííûé òîì ïîääåðæèâàåò èìåíà ôàéëîâ ñ ó÷åòîì ðåãèñòðà\n");
			if (lpFileSystemFlags & FILE_CASE_PRESERVED_NAMES)
				printf("Óêàçàííûé òîì ïîääåðæèâàåò ñîõðàíåííûé ðåãèñòð èìåí ôàéëîâ ïðè ðàñïîëîæåíèè èìåíè íà äèñêå\n");
			if (lpFileSystemFlags & FILE_UNICODE_ON_DISK)
				printf("Óêàçàííûé òîì ïîääåðæèâàåò Þíèêîä â èìåíàõ ôàéëîâ, êàê îíè îòîáðàæàþòñÿ íà äèñêå\n");
			if (lpFileSystemFlags & FILE_PERSISTENT_ACLS)
				printf("Óêàçàííûé òîì ñîõðàíÿåò è ïðèìåíÿåò ñïèñêè óïðàâëåíèÿ äîñòóïîì (ACL)\n");
			if (lpFileSystemFlags & FILE_FILE_COMPRESSION)
				printf("Óêàçàííûé òîì ïîääåðæèâàåò ñæàòèå íà îñíîâå ôàéëîâ\n");
			if (lpFileSystemFlags & FILE_VOLUME_QUOTAS)
				printf("Óêàçàííûé òîì ïîääåðæèâàåò êâîòû äèñêîâ\n");
			if (lpFileSystemFlags & FILE_SUPPORTS_SPARSE_FILES)
				printf("Óêàçàííûé òîì ïîääåðæèâàåò ðàçðåæåííûå ôàéëû\n");
			if (lpFileSystemFlags & FILE_SUPPORTS_REPARSE_POINTS)
				printf("Óêàçàííûé òîì ïîääåðæèâàåò òî÷êè ïîâòîðíîãî àíàëèçà\n");
			if (lpFileSystemFlags & FILE_SUPPORTS_REMOTE_STORAGE)
				printf("Óêàçàííûé òîì ïîääåðæèâàåò óäàëåííîå õðàíèëèùå\n");
			if (lpFileSystemFlags & FILE_VOLUME_IS_COMPRESSED)
				printf("Óêàçàííûé òîì ÿâëÿåòñÿ ñæàòûì òîìîì\n");
			if (lpFileSystemFlags & FILE_SUPPORTS_OBJECT_IDS)
				printf("Óêàçàííûé òîì ïîääåðæèâàåò èäåíòèôèêàòîðû îáúåêòîâ\n");
			if (lpFileSystemFlags & FILE_SUPPORTS_ENCRYPTION)
				printf("Óêàçàííûé òîì ïîääåðæèâàåò çàøèôðîâàííóþ ôàéëîâóþ ñèñòåìó (EFS)\n");
			if (lpFileSystemFlags & FILE_NAMED_STREAMS)
				printf("Óêàçàííûé òîì ïîääåðæèâàåò èìåíîâàííûå ïîòîêè\n");
			if (lpFileSystemFlags & FILE_READ_ONLY_VOLUME)
				printf("Óêàçàííûé òîì äîñòóïåí òîëüêî äëÿ ÷òåíèÿ\n");
			if (lpFileSystemFlags & FILE_SEQUENTIAL_WRITE_ONCE)
				printf("Óêàçàííûé òîì ïîääåðæèâàåò îäíó ïîñëåäîâàòåëüíóþ çàïèñü\n");
			if (lpFileSystemFlags & FILE_SUPPORTS_TRANSACTIONS)
				printf("Óêàçàííûé òîì ïîääåðæèâàåò òðàíçàêöèè\n");
			if (lpFileSystemFlags & FILE_SUPPORTS_HARD_LINKS)
				printf("Óêàçàííûé òîì ïîääåðæèâàåò æåñòêèå ñâÿçè\n");
			if (lpFileSystemFlags & FILE_SUPPORTS_EXTENDED_ATTRIBUTES)
				printf("Óêàçàííûé òîì ïîääåðæèâàåò ðàñøèðåííûå àòðèáóòû\n");
			if (lpFileSystemFlags & FILE_SUPPORTS_OPEN_BY_FILE_ID)
				printf("Ôàéëîâàÿ ñèñòåìà ïîääåðæèâàåò open by FileID\n");
			if (lpFileSystemFlags & FILE_SUPPORTS_USN_JOURNAL)
				printf("Óêàçàííûé òîì ïîääåðæèâàåò æóðíàëû îáíîâëåíèÿ ïîðÿäêîâîãî íîìåðà (USN)\n");
			if (lpFileSystemFlags & FILE_SUPPORTS_BLOCK_REFCOUNTING)
				printf("Óêàçàííûé òîì ïîääåðæèâàåò ñîâìåñòíîå èñïîëüçîâàíèå ëîãè÷åñêèõ êëàñòåðîâ ìåæäó ôàéëàìè íà îäíîì òîìå\n");
			if (lpFileSystemFlags & FILE_DAX_VOLUME)
				printf("Óêàçàííûé òîì ÿâëÿåòñÿ ïðÿìûì äîñòóïîì (DAX)\n");
			//
		}
		else printf("Îøèáêà çàïðîñà\n");
		break;
	case 3:
		res = GetDiskFreeSpace(l, &lpSectorsPerCluster, &lpBytesPerSector, &lpNumberOfFreeClusters, &lpTotalNumberOfClusters);
		if (res) {
			printf(
				"Êîëè÷åñòâî ñåêòîðîâ â êëàñòåðå: %u\n"
				"Êîëè÷åñòâî áàéò â ñåêòîðå: %u\n"
				"Êîëè÷åñòâî ñâîáîäíûõ êëàñòåðîâ: %u\n"
				"Êîëè÷åñòâî êëàñòåðîâ: %u\n",
				lpSectorsPerCluster,
				lpBytesPerSector,
				lpNumberOfFreeClusters,
				lpTotalNumberOfClusters);
		}
		else printf("Îøèáêà çàïðîñà\n");
		break;
	default:
		break;
	}
}
// CreateDirectory, RemoveDirectory
void CreateDeleteDirectory() {
	string tmp;
	wstring wtmp;
	LPCWSTR l;
	bool res;
	int val;
	printf(
		"Ñîçäàíèå è óäàëåíèå êàòàëîãîâ: \n"
		"1 - ñîçäàòü êàòàëîã\n"
		"2 - óäàëèòü êàòàëîã\n");
	cin >> tmp;
	val = _strtoi64(tmp.c_str(), nullptr, 10); // convert string to int
	switch (val)
	{
	case 1:
		printf("Ââåäèòå ïîëíîå íàçâàíèå êàòàëîãà (C:\\folder_name): ");
		cin >> tmp;
		// convert string to LPCWSTR
		wtmp = wstring(tmp.begin(), tmp.end());
		l = wtmp.c_str();
		//
		res = CreateDirectory(l, nullptr);
		if (res) {
			printf("Êàòàëîã óñïåøíî ñîçäàí\n");
		}
		else printf("Îøèáêà ñîçäàíèÿ êàòàëîãà\n");
		break;
	case 2:
		printf("Ââåäèòå ïîëíîå íàçâàíèå êàòàëîãà (C:\\folder_name): ");
		cin >> tmp;
		// convert string to LPCWSTR
		wtmp = wstring(tmp.begin(), tmp.end());
		l = wtmp.c_str();
		//
		res = RemoveDirectory(l);
		if (res) {
			printf("Êàòàëîã óñïåøíî óäàëåí\n");
		}
		else printf("Îøèáêà óäàëåíèÿ êàòàëîãà\n");
		break;
	default:
		break;
	}
}
// CreateFile
void CreateFileInDirectory() {
	string tmp;
	wstring wtmp;
	LPCWSTR l;
	bool res;
	printf("Ñîçäàíèå ôàéëà: \n");
	printf("Ââåäèòå ïîëíîå íàçâàíèå ôàéëà (C:\\folder_name\\file_name.extension): ");
	cin >> tmp;
	// convert string to LPCWSTR
	wtmp = wstring(tmp.begin(), tmp.end());
	l = wtmp.c_str();
	//
	res = CreateFile(l, GENERIC_ALL, FILE_SHARE_DELETE | FILE_SHARE_READ | FILE_SHARE_WRITE, NULL, CREATE_NEW, FILE_ATTRIBUTE_NORMAL, NULL);
	if (res) {
		printf("Ôàéë óñïåøíî ñîçäàí\n");
	}
	else printf("Îøèáêà ñîçäàíèÿ ôàéëà\n");
}
// CopyFile, MoveFile, MoveFileEx
void CopyMoveFile() {
	string tmp, firsttmp, secondtmp;
	int val;
	wstring wfirsttmp, wsecondtmp;
	LPCWSTR first, second;
	DWORD res;
	printf(
		"Êîïèðîâàíèå è ïåðåìåùåíèå ôàéëîâ: \n"
		"1 - êîïèðîâàòü ôàéë\n"
		"2 - ïåðåìåñòèòü ôàéë\n"
		"3 - ïåðåìåñòèòü ôàéë ñ ïðîâåðêîé íà èìåíà\n");
	cin >> tmp;
	val = _strtoi64(tmp.c_str(), nullptr, 10); // convert string to int
	printf("Ââåäèòå ïîëíûé èñõîäíûé ïóòü (C:\\folder_name\\file_name.extention): ");
	cin >> firsttmp;
	// convert string to LPCWSTR
	wfirsttmp = wstring(firsttmp.begin(), firsttmp.end());
	first = wfirsttmp.c_str();
	//
	printf("Ââåäèòå ïîëíûé êîíå÷íûé ïóòü: ");
	cin >> secondtmp;
	// convert string to LPCWSTR
	wsecondtmp = wstring(secondtmp.begin(), secondtmp.end());
	second = wsecondtmp.c_str();
	//
	switch (val)
	{
	case 1:
		res = CopyFile(first, second, false);
		if (res) {
			printf("Ôàéë óñïåøíî ñêîïèðîâàí\n");
		}
		else printf("Îøèáêà êîïèðîâàíèÿ ôàéëà\n");
		break;
	case 2:
		res = MoveFile(first, second);
		if (res) {
			printf("Ôàéë óñïåøíî ïåðåìåùåí\n");
		}
		else printf("Îøèáêà ïåðåìåùåíèÿ ôàéëà\n");
		break;
	case 3:
		res = MoveFileEx(first, second, NULL);
		if (res) {
			printf("Ôàéë óñïåøíî ïåðåìåùåí\n");
		}
		else {
			res = GetLastError();
			if (GetLastError() == ERROR_ALREADY_EXISTS) {
				printf("Ôàéë ñóùåñòâóåò, çàìåíèòü åãî? (Y/N): ");
				while (tmp != "Y" && tmp != "N") {
					cin >> tmp;
					if (tmp != "Y" && tmp != "N") printf("Ââåäèòå Y èëè N\n");
				}
				if (tmp == "Y") {
					res = MoveFileEx(first, second, MOVEFILE_REPLACE_EXISTING);
					if (res) {
						printf("Ôàéë óñïåøíî ïåðåìåùåí\n");
					}
					else printf("Îøèáêà ïåðåìåùåíèÿ ôàéëà\n");
				}
			}
		}
		break;
	default:
		break;
	}
}
// GetFileAttributes, SetFileAttributes, GetFileInformationByHandle, GetFileTime, SetFileTime
void FileAttributes() {
	string tmp, tmpfilename;
	int val, year, month, day, hour, minute, second, millisecond;
	wstring wtmpfilename;
	LPCWSTR filename;
	DWORD res, fileAttr;
	HANDLE handler;
	BY_HANDLE_FILE_INFORMATION handlerRes;
	FILETIME create, lastAccess, lastWrite;
	SYSTEMTIME sysTime;
	printf(
		"Àòðèáóòû ôàéëîâ: \n"
		"1 - ïîëó÷èòü àòðèáóòû ôàéëà\n"
		"2 - óñòàíîâèòü àòðèáóòû ôàéëà\n"
		"3 - ïîëó÷èòü èíôîðìàöèþ î ôàéëå ïî äåñêðèïòîðó\n"
		"4 - èíôîðìàöèÿ î âðåìåíè ôàéëà\n"
		"5 - èçìåíèòü àòðèáóòû âðåìåíè ôàéëà\n");
	cin >> tmp;
	val = _strtoi64(tmp.c_str(), nullptr, 10); // convert string to int
	printf("Ââåäèòå ïîëíîå íàçâàíèå ôàéëà (C:\\folder_name\\file_name.extention): ");
	cin >> tmpfilename;
	// convert string to LPCWSTR
	wtmpfilename = wstring(tmpfilename.begin(), tmpfilename.end());
	filename = wtmpfilename.c_str();
	//
	switch (val)
	{
	case 1:
		res = GetFileAttributes(filename);
		if (res) {
			printf("Àòðèáóòû ôàéëà: \n");
			FileAttrOut(res);
		}
		else printf("Îøèáêà â ïîëó÷åíèè àòðèáóòîâ ôàéëà\n");
		break;
	case 2:
		printf(
			"Óñòàíîâèòü àòðèáóòû. Ââåäèòå ïîñëåäîâàòåëüíîñòü ÷åðåç ïðîáåë, îêàí÷èâàþùóþñÿ íà 0 (1 2 5 0): \n"
			"1 - Àðõèâíûé ôàéë èëè êàòàëîã\n"
			"2 - Ôàéë èëè êàòàëîã ñêðûò\n"
			"3 - Ôàéë, êîòîðûé íå èìååò äðóãèõ àòðèáóòîâ\n"
			"4 - Ôàéë èëè êàòàëîã íå èíäåêñèðóþòñÿ ñëóæáîé èíäåêñèðîâàíèÿ ñîäåðæèìîãî\n"
			"5 - Äàííûå ôàéëà íåäîñòóïíû íåìåäëåííî\n"
			"6 - Ôàéë, äîñòóïíûé òîëüêî äëÿ ÷òåíèÿ\n"
			"7 - Ôàéë èëè êàòàëîã, êîòîðûé îïåðàöèîííàÿ ñèñòåìà èñïîëüçóåò ÷àñòè÷íî èëè ïîëíîñòüþ\n"
			"8 - Ôàéë, èñïîëüçóåìûé äëÿ âðåìåííîãî õðàíèëèùà\n");
		res = 0;
		while (val != 0)
		{
			cin >> tmp;
			val = _strtoi64(tmp.c_str(), nullptr, 10); // convert string to int
			switch (val)
			{
			case 0:
				break;
			case 1:
				res |= FILE_ATTRIBUTE_ARCHIVE;
				break;
			case 2:
				res |= FILE_ATTRIBUTE_HIDDEN;
				break;
			case 3:
				res |= FILE_ATTRIBUTE_NORMAL;
				break;
			case 4:
				res |= FILE_ATTRIBUTE_NOT_CONTENT_INDEXED;
				break;
			case 5:
				res |= FILE_ATTRIBUTE_OFFLINE;
				break;
			case 6:
				res |= FILE_ATTRIBUTE_READONLY;
				break;
			case 7:
				res |= FILE_ATTRIBUTE_SYSTEM;
				break;
			case 8:
				res |= FILE_ATTRIBUTE_TEMPORARY;
				break;
			default:
				printf("Îøèáêà, ââåäåíî íåâåðíîå ÷èñëî\n");
				val = 0;
				res = 0;
				break;
			}
		}
		res = SetFileAttributes(filename, res);
		if (res) {
			printf("Àòðèáóòû óñïåøíî óñòàíîâëåíû\n");
		}
		else printf("Îøèáêà óñòàíîâëåíèÿ àòðèáóòîâ\n");
		break;
	case 3:
		handler = CreateFile(filename, GENERIC_READ, FILE_SHARE_READ, NULL, OPEN_EXISTING, NULL, NULL);
		res = GetFileInformationByHandle(handler, &handlerRes);
		if (res) {
			fileAttr = handlerRes.dwFileAttributes;
			printf("Àòðèáóòû ôàéëà ïî äåñêðèïòîðó: \n");
			FileAttrOut(fileAttr);
			printf(
				"Èíôîðìàöèÿ î ôàéëå: \n"
				"Ñåðèéíûé íîìåð òîìà, ñîäåðæàùåãî ôàéë: %u\n"
				"Êîëè÷åñòâî ññûëîê íà ýòîò ôàéë: %u\n"
				"Íèæíèé ïîðÿäîê ÷àñòè ðàçìåðà ôàéëà: %u\n"
				"Âåðõíèé ïîðÿäîê ÷àñòè ðàçìåðà ôàéëà: %u\n"
				"Íèæíèé ïîðÿäîê èäåíòèôèêàòîðà, ñâÿçàííîãî ñ ôàéëîì: %u\n"
				"Âåðõíèé ïîðÿäîê èäåíòèôèêàòîðà, ñâÿçàííîãî ñ ôàéëîì: %u\n",
				handlerRes.dwVolumeSerialNumber,
				handlerRes.nNumberOfLinks,
				handlerRes.nFileSizeLow,
				handlerRes.nFileSizeHigh,
				handlerRes.nFileIndexLow,
				handlerRes.nFileIndexHigh);
		}
		else printf("Îøèáêà ÷òåíèÿ àòðèáóòîâ ôàéëà\n");
		CloseHandle(handler);
		break;
	case 4:
		handler = CreateFile(filename, GENERIC_READ, FILE_SHARE_READ, NULL, OPEN_EXISTING, NULL, NULL);
		res = GetFileTime(handler, &create, &lastAccess, &lastWrite);
		if (res) {
			FileTimeToSystemTime(&create, &sysTime);
			printf("Âðåìÿ ñîçäàíèÿ ôàéëà: %i:%i:%i.%i %i.%i.%i\n", sysTime.wHour, sysTime.wMinute, sysTime.wSecond, sysTime.wMilliseconds, sysTime.wDay, sysTime.wMonth, sysTime.wYear);
			FileTimeToSystemTime(&lastAccess, &sysTime);
			printf("Âðåìÿ ïîñëåäíåãî äîñòóïà ê ôàéëó: %i:%i:%i.%i %i.%i.%i\n", sysTime.wHour, sysTime.wMinute, sysTime.wSecond, sysTime.wMilliseconds, sysTime.wDay, sysTime.wMonth, sysTime.wYear);
			FileTimeToSystemTime(&lastWrite, &sysTime);
			printf("Âðåìÿ ïîñëåäíåãî èçìåíåíèÿ ôàéëà: %i:%i:%i.%i %i.%i.%i\n", sysTime.wHour, sysTime.wMinute, sysTime.wSecond, sysTime.wMilliseconds, sysTime.wDay, sysTime.wMonth, sysTime.wYear);
		}
		else printf("Îøèáêà â ÷òåíèè âðåìåíè ôàéëà\n");
		break;
	case 5:
		handler = CreateFile(filename, FILE_WRITE_ATTRIBUTES, FILE_SHARE_READ | FILE_SHARE_WRITE, NULL, OPEN_EXISTING, NULL, NULL);
		printf(
			"Èçìåíåíèå àòðèáóòà âðåìåíè ôàéëà: \n"
			"1 - èçìåíèòü âðåìÿ ñîçäàíèÿ ôàéëà\n"
			"2 - èçìåíèòü âðåìÿ ïîñëåäíåãî äîñòóïà ê ôàéëó\n"
			"3 - èçìåíèòü âðåìÿ ïîñëåäíåãî èçìåíåíèÿ ôàéëà\n");
		cin >> tmp;
		val = _strtoi64(tmp.c_str(), nullptr, 10); // convert string to int
		printf("Ââåäèòå ãîä: ");
		cin >> tmp;
		year = _strtoi64(tmp.c_str(), nullptr, 10); // convert string to int
		printf("Ââåäèòå ìåñÿö: ");
		cin >> tmp;
		month = _strtoi64(tmp.c_str(), nullptr, 10); // convert string to int
		printf("Ââåäèòå äåíü: ");
		cin >> tmp;
		day = _strtoi64(tmp.c_str(), nullptr, 10); // convert string to int
		printf("Ââåäèòå ÷àñ: ");
		cin >> tmp;
		hour = _strtoi64(tmp.c_str(), nullptr, 10); // convert string to int
		printf("Ââåäèòå ìèíóòó: ");
		cin >> tmp;
		minute = _strtoi64(tmp.c_str(), nullptr, 10); // convert string to int
		printf("Ââåäèòå ñåêóíäó: ");
		cin >> tmp;
		second = _strtoi64(tmp.c_str(), nullptr, 10); // convert string to int
		printf("Ââåäèòå ìèëëèñåêóíäó: ");
		cin >> tmp;
		millisecond = _strtoi64(tmp.c_str(), nullptr, 10); // convert string to int
		sysTime.wYear = year;
		sysTime.wMonth = month;
		sysTime.wDay = day;
		sysTime.wHour = hour;
		sysTime.wMinute = minute;
		sysTime.wSecond = second;
		sysTime.wMilliseconds = millisecond;
		SystemTimeToFileTime(&sysTime, &create);
		switch (val) {
		case 1:
			res = SetFileTime(handler, &create, NULL, NULL);
			break;
		case 2:
			res = SetFileTime(handler, NULL, &create, NULL);
			break;
		case 3:
			res = SetFileTime(handler, NULL, NULL, &create);
			break;
		default:
			res = 0;
			break;
		}
		if (res) {
			printf("Âðåìÿ ôàéëà óñïåøíî èçìåíåíî\n");
		}
		else printf("Îøèáêà â èçìåíåíèè âðåìåíè ôàéëà\n");
		break;
	default:
		break;
	}
}
// task 2 - APC
void CopyFileTask() {
	int blockSize, operations, totalBlockSize, countIter;
	long fileSize, finalFileSize;
	char* buffer1, * buffer2, * pToBuf;
	bool flagFirstEnterRead = false, flagFirstEnterWrite = false;
	string tmp1, tmp2;
	wstring wpath1, wpath2;
	LPCWSTR path1, path2;
	HANDLE handle1, handle2;
	DWORD timeStart, timeEnd, err;
	OVERLAPPED* overlapped1, * overlapped2;
	printf("Ââåäèòå ðàçìåð áëîêà: 4096 * "); // disk has sector size = 512, number of sectors in cluster = 8, 512 * 8 = 4096
	cin >> blockSize;
	printf("Ââåäèòå ÷èñëî îïåðàöèé: ");
	cin >> operations;
	printf("Ââåäèòå ïîëíûé ïóòü ïåðâîãî ôàéëà (C:\\folder\\filename.extention): ");
	cin >> tmp1;
	// convert string to LPCWSTR
	wpath1 = wstring(tmp1.begin(), tmp1.end());
	path1 = wpath1.c_str();
	//
	printf("Ââåäèòå ïîëíûé ïóòü âòîðîãî ôàéëà (C:\\folder\\filename.extention): ");
	cin >> tmp2;
	// convert string to LPCWSTR
	wpath2 = wstring(tmp2.begin(), tmp2.end());
	path2 = wpath2.c_str();
	//
	// main script
	timeStart = GetTickCount(); // time
	totalBlockSize = 4096 * blockSize;
	// create handlers
	handle1 = CreateFile(path1, GENERIC_READ, NULL, NULL, OPEN_EXISTING, FILE_FLAG_NO_BUFFERING | FILE_FLAG_OVERLAPPED, NULL);
	handle2 = CreateFile(path2, GENERIC_READ | GENERIC_WRITE, NULL, NULL, CREATE_ALWAYS, FILE_FLAG_NO_BUFFERING | FILE_FLAG_OVERLAPPED, NULL);
	//
	fileSize = GetFileSize(handle1, NULL);
	finalFileSize = fileSize; // save final size for later
	pToBuf = new char[totalBlockSize * operations]; // poiter to buffer
	overlapped1 = new OVERLAPPED[operations];
	overlapped2 = new OVERLAPPED[operations];
	for (int i = 0; i < operations; ++i) {
		overlapped1[i].Offset = 0;
		overlapped1[i].OffsetHigh = 0;
		overlapped1[i].hEvent = NULL;
		overlapped2[i].Offset = 0;
		overlapped2[i].OffsetHigh = 0;
		overlapped2[i].hEvent = NULL;
	}
	do {
		completions = 0; // completed Ex-operations
		countIter = 0; // count executed Ex-operations (end of file may require less operations to read)
		// return buffers to their start positions
		buffer1 = pToBuf;
		buffer2 = pToBuf;
		//
		for (int i = 0; i < operations; ++i) {
			if (fileSize > 0) {
				++countIter;
				if (flagFirstEnterRead) { // we don't need to push offsets for the first read
					for (int j = 0; j < operations; ++j) {
						overlapped1[j].Offset += totalBlockSize; // push all offsets to the number of executed Ex-operations
					}
				}
				if (i) buffer1 += totalBlockSize; // we don't need to shift buffer for first in loop read
				flagFirstEnterRead = true;
				ReadFileEx(handle1, buffer1, totalBlockSize, &overlapped1[i], Completion);
				fileSize -= totalBlockSize;
			}
		}
		while (completions < countIter) // wait for all reads
			SleepEx(-1, true);
		completions = 0;
		for (int i = 0; i < operations; ++i) {
			if (flagFirstEnterWrite) { // we don't need to push offsets for the first write
				for (int j = 0; j < operations; ++j) {
					overlapped2[j].Offset += totalBlockSize; // push all offsets to the number of executed Ex-operations
				}
			}
			if (i) buffer2 += totalBlockSize; // we don't need to shift buffer for first in loop write
			flagFirstEnterWrite = true;
			WriteFileEx(handle2, buffer2, totalBlockSize, &overlapped2[i], Completion);
		}
		while (completions < countIter) // wait for all writes
			SleepEx(-1, true);
	} while (fileSize > 0);
	SetFilePointer(handle2, finalFileSize, NULL, FILE_BEGIN);
	SetEndOfFile(handle2);
	timeEnd = GetTickCount(); // time end
	err = GetLastError();
	if (err == ERROR_SUCCESS || err == ERROR_ALREADY_EXISTS) // createfile func drops unnecessary in our case ERROR_ALREADY_EXISTS error
		printf("Ôàéë óñïåøíî ñêîïèðîâàí\n");
	else
		printf("Êîïèðîâàíèå ôàéëà çàâåðøèëîñü ñ îøèáêîé: %i\n", err);
	//
	printf("Çàòðà÷åíî âðåìåíè: %.3f\n", ((float)(timeEnd - timeStart)/1000));
	CloseHandle(handle1);
	CloseHandle(handle2);
}
