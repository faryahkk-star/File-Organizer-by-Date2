from pathlib import Path
from datetime import datetime
import shutil


class FileOrganizer:

    def __init__(selff, directory):
        self.directory = Path(directory)
        self.moved = 0
        self.skipped = 0

    def organize(self):

        for file in self.directory.iterdir():

            if not file.is_file():
                continue

            try:
                modified = datetime.fromtimestamp(
                    file.stat().st_mtime
                )

                folder_name = modified.strftime("%Y-%m")

                destination = self.directory / folder_name
                destination.mkdir(exist_ok=True)

                target = destination / file.name

                # جلوگیری از بازنویسی فایل
                if target.exists():

                    stem = file.stem
                    suffix = file.suffix
                    counter = 1

                    while target.exists():
                        target = destination / f"{stem}_{counter}{suffix}"
                        counter += 1

                shutil.move(str(file), str(target))
                self.moved += 1

            except Exception as e:
                print(f"Error: {file.name} -> {e}")
                self.skipped += 1

    def report(self):

        print("\n====== REPORT ======")
        print(f"Moved files : {self.moved}")
        print(f"Skipped     : {self.skipped}")


def main():

    path = input("Folder path: ").strip()

    organizer = FileOrganizer(path)

    print("\nOrganizing files...\n")

    organizer.organize()

    organizer.report()


if __name__ == "__main__":
    main()
