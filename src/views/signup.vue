<template>
  <div>
    <v-app id="background">
      <v-content>
        <v-row align="center" justify="center">
          <v-col md="auto">
            <img src="../assets/home-logo.png" width="250px" height="auto" />
          </v-col>
        </v-row>
        <v-row align="center" justify="center">
          <v-col md="auto">
            <v-card color="hsl(220, 8%, 23%)">
              <v-card-title class="justify-center" id="title">Create an account</v-card-title>
              <v-card-actions class="justify-center">
                <v-form ref="form" v-model="valid" lazy-validation>
                  <v-text-field
                    dark
                    v-model="username"
                    :rules="usernameRules"
                    outlined
                    label="Username"
                    required
                  ></v-text-field>

                  <v-text-field
                    dark
                    v-model="password"
                    :rules="passwordRules"
                    :append-icon="show1 ? 'mdi-eye' : 'mdi-eye-off'"
                    :type="show1 ? 'text' : 'password'"
                    @click:append="show1 = !show1"
                    outlined
                    label="Password"
                    required
                    style="margin-top: -20px;"
                  ></v-text-field>

                  <v-text-field
                    dark
                    v-model="rePassword"
                    :rules="rePasswordRules"
                    :append-icon="show2 ? 'mdi-eye' : 'mdi-eye-off'"
                    :type="show2 ? 'text' : 'password'"
                    @click:append="show2 = !show2"
                    outlined
                    label="Re-enter password"
                    required
                    style="margin-top: -20px;"
                  ></v-text-field>

                  <v-text-field
                    dark
                    v-model="email"
                    :rules="emailRules"
                    outlined
                    label="Email"
                    required
                    style="margin-top: -20px;"
                  ></v-text-field>

                  <v-text-field
                    dark
                    v-model="fullName"
                    :rules="fullNameRules"
                    outlined
                    label="Full name"
                    required
                    style="margin-top: -20px;"
                  ></v-text-field>

                  <v-text-field
                    dark
                    v-model="studentID"
                    :rules="studentIDRules"
                    outlined
                    label="Student ID"
                    required
                    style="margin-top: -20px;"
                  ></v-text-field>

                  <v-text-field
                    dark
                    v-model="university"
                    :rules="universityRules"
                    outlined
                    label="University"
                    required
                    style="margin-top: -20px;"
                  ></v-text-field>

                  <v-text-field
                    dark
                    v-model="citizenID"
                    :rules="citizenIDRules"
                    outlined
                    label="Citizen ID"
                    required
                    style="margin-top: -20px;"
                  ></v-text-field>

                  <v-btn
                    class="white--text"
                    color="primary"
                    :disabled="!valid"
                    @click="register"
                  >Continue</v-btn>
                  <div class="termOfUse">
                    <p>
                      By continuing, you have agreed to DMS's
                      <b>Term of Service</b> and
                      <b>Privacy Policy</b>
                    </p>
                  </div>
                </v-form>
              </v-card-actions>
            </v-card>
          </v-col>
        </v-row>
        <!--
        <v-dialog v-model="isCompleted" width="400px" height="200px">
          <v-card width="800px" height="200px">
            <v-card-title style="font-size: 30px;"> Register successfully</v-card-title>
            <v-card-subtitle style="font-size: 20px; margin-top: 2px;"> Press continue to sign in</v-card-subtitle>
            <v-card-actions>
              <v-btn
                style="width: 90%; margin-left: 18px;"
                color="primary"
                @click="register"
                to="/sign-in"
              >
                CONTINUE
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        -->
      </v-content>
    </v-app>
  </div>
</template>

<script>
const axios = require("axios");

export default {
  data() {
    return {
      isCompleted: false,
      valid: true,
      show1: false,
      show2: false,
      username: "",
      password: "",
      rePassword: "",
      email: "",
      fullName: "",
      studentID: "",
      university: "",
      citizenID: "",
      numberOfStudent: 0,
      usernameRules: [
        (v) => !!v || "User is required",
        (v) => (v && v.length <= 25) || "Name must be less than 25 characters",
      ],
      passwordRules: [
        (v) => !!v || "Password is required",
        (v) =>
          (v && v.length >= 6) || "Password must have at least 6 characters",
      ],
      rePasswordRules: [
        (v) => !!v || "Re-enter password is required",
        (v) => v === this.password || "Password do not match",
      ],
      emailRules: [
        (v) => !!v || "E-mail is required",
        (v) => /.+@.+\..+/.test(v) || "E-mail must be valid",
      ],
      fullNameRules: [(v) => !!v || "Full name is required"],
      studentIDRules: [(v) => !!v || "Student ID is required"],
      universityRules: [(v) => !!v || "University is required"],
      citizenIDRules: [(v) => !!v || "Citizen ID is required"],
    };
  },
  created() {
    this.getNumber();
  },
  methods: {
    getNumber() {
      axios
        .get("http://admin-database.herokuapp.com/student/getAll")
        .then((Response) => {
          this.numberOfStudent = Response.data.length;
          console.log(this.numberOfStudent);
        });
    },
    register() {
      console.log("CLICK SUCCESSFULLY");
      this.$refs.form.validate();
      let config = {
        headers: {
          "Content-Type": "application/json",
        },
      };
      let data = {
        username: this.username,
        password: this.password,
        email: this.email,
        name: this.fullName,
        studentId: this.studentID,
        university: this.university,
        citizenId: this.citizenID,
      };
      axios.post("http://admin-database.herokuapp.com/student/addNewStudent", data, config)
      .then((Response) => Response.data[this.numberOfStudent + 1])
      .then(({ username, password, email, name, studentId, university, citizenId }) => {
          this.username = username;
          this.password = password;
          this.email = email;
          this.fullName = name;
          this.studentID = studentId;
          this.citizenID = citizenId;
          this.university = university;
        }
      );
      alert("Sign up successfully, click OK to sign in")
      this.$router.replace("/sign-in")
    },
  },
};
</script>

<style scoped>
@import url("https://fonts.googleapis.com/css?family=Quicksand");
@import url("https://fonts.googleapis.com/css2?family=Roboto");

.termOfUse {
  color: hsl(227, 58%, 65%);
  margin-top: 10px;
  font-size: 10px;
}

#title {
  font-family: "Roboto", sans-serif;
  color: white;
  font-size: 24px;
}

#background {
  background-image: url("../assets/tsignup_img.jpg");
  background-size: cover;
  height: 100%;
}

.v-btn {
  width: 100%;
  background-color: hsl(227, 58%, 65%);
}

.v-card {
  position: flex;
  width: 440px;
  height: 44rem;
}
</style>

