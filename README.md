// env_manager.ts
import * as fs from "fs";
import * as readline from "readline";

class EnvManager {
  private envFile = ".env";

  constructor(filePath?: string) {
    if (filePath) this.envFile = filePath;
  }

  list(): void {
    if (!fs.existsSync(this.envFile)) {
      console.log("❌ No .env file found.");
      return;
    }
    const content = fs.readFileSync(this.envFile, "utf-8");
    console.log("\n📜 Environment Variables:");
    console.log(content || "No variables set.");
  }

  add(key: string, value: string): void {
    fs.appendFileSync(this.envFile, `\n${key}=${value}`);
    console.log(`✅ Added: ${key}=${value}`);
  }

  edit(key: string, newValue: string): void {
    if (!fs.existsSync(this.envFile)) {
      console.log("❌ No .env file found.");
      return;
    }
    let content = fs.readFileSync(this.envFile, "utf-8");
    const regex = new RegExp(`^${key}=.*$`, "m");

    if (regex.test(content)) {
      content = content.replace(regex, `${key}=${newValue}`);
    } else {
      content += `\n${key}=${newValue}`;
    }
    fs.writeFileSync(this.envFile, content);
    console.log(`✏️ Updated: ${key}=${newValue}`);
  }

  validate(requiredKeys: string[]): void {
    if (!fs.existsSync(this.envFile)) {
      console.log("❌ No .env file found.");
      return;
    }
    const content = fs.readFileSync(this.envFile, "utf-8");
    const keys = content.split("\n").map(line => line.split("=")[0]);

    let missing = requiredKeys.filter(k => !keys.includes(k));
    if (missing.length > 0) {
      console.log("⚠️ Missing keys:", missing.join(", "));
    } else {
      console.log("✅ All required keys are present!");
    }
  }
}

// Example usage
const manager = new EnvManager();
manager.list();
manager.add("API_KEY", "12345");
manager.edit("API_KEY", "67890");
manager.validate(["API_KEY", "DB_URL"]);
