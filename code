import os
import hashlib
import json

class FileIntegrityChecker:
    def __init__(self, directory, hash_file="file_hashes.json"):
        self.directory = directory
        self.hash_file = hash_file

    def calculate_hash(self, file_path):
        """Calculate SHA-256 hash of a file."""
        sha256_hash = hashlib.sha256()
        try:
            with open(file_path, "rb") as f:
                for byte_block in iter(lambda: f.read(4096), b""):
                    sha256_hash.update(byte_block)
            return sha256_hash.hexdigest()
        except FileNotFoundError:
            print(f"File not found: {file_path}")
            return None

    def generate_file_hashes(self):
        """Generate and save hashes for all files in the directory."""
        file_hashes = {}
        for root, _, files in os.walk(self.directory):
            for file in files:
                file_path = os.path.join(root, file)
                file_hashes[file_path] = self.calculate_hash(file_path)

        with open(self.hash_file, "w") as f:
            json.dump(file_hashes, f, indent=4)
        print(f"Hashes saved to {self.hash_file}")

    def verify_file_integrity(self):
        """Verify the integrity of files against saved hashes."""
        if not os.path.exists(self.hash_file):
            print(f"Hash file not found: {self.hash_file}")
            return

        with open(self.hash_file, "r") as f:
            saved_hashes = json.load(f)

        for file_path, saved_hash in saved_hashes.items():
            current_hash = self.calculate_hash(file_path)
            if current_hash is None:
                print(f"File missing: {file_path}")
            elif current_hash != saved_hash:
                print(f"File modified: {file_path}")
            else:
                print(f"File verified: {file_path}")

if __name__ == "__main__":
    import argparse

    parser = argparse.ArgumentParser(description="File Integrity Checker")
    parser.add_argument("directory", help="Directory to monitor")
    parser.add_argument("--generate", action="store_true", help="Generate file hashes")
    parser.add_argument("--verify", action="store_true", help="Verify file integrity")

    args = parser.parse_args()

    checker = FileIntegrityChecker(args.directory)

    if args.generate:
        checker.generate_file_hashes()
    elif args.verify:
        checker.verify_file_integrity()
    else:
        print("Please specify --generate or --verify.")
